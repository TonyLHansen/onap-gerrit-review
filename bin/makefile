SHSRC=	onap-gerrit-review onap-gerrit-review-step2
PYSRC=	onap-gerrit-review-extract-xml \
	onap-gerrit-review-find-file-type \
	onap-gerrit-review-find-leading-tabs \
	onap-gerrit-review-print-lines \
	onap-gerrit-review-rename-files \
	onap-gerrit-review-verify-eol \
	onap-gerrit-review-verify-json \
	onap-gerrit-review-verify-releasefile \
	onap-gerrit-review-verify-xml \
	onap-gerrit-review-verify-yaml

none:
	@echo make tests - run run-shellcheck, run-pylint, run-whitespace-check, messages
	@echo make run-shellcheck -- check shell scripts
	@echo make run-pylint -- check python scripts
	@echo make run-whitespace-check -- look for trailing whitespace
	@echo make messages -- check the messages against warnings-and-errors.md

tests: run-black run-shellcheck run-pylint messages

run-black:
	black -l120 $(PYSRC)

run-shellcheck:
	shellcheck -f gcc $(SHSRC) | sed 's/ note:/ warning: note:/'

run-pylint:
	for i in $(PYSRC); do pylint $$i; done

run-whitespace-check:
	if grep -n '[[:space:]]$$' $(SHSRC) $(PYSRC); then exit 1; else exit 0; fi


MESSAGES=../warnings-and-errors.md
OGR2=onap-gerrit-review-step2

messages:
	@TMP1=$$(mktemp); TMP2=$$(mktemp); \
	TMP3=$$(mktemp); TMP4=$$(mktemp); \
	trap 'rm -f "$$TMP1" "$$TMP2" "$$TMP3" "$$TMP4"' 0 1 2 3 15; \
	egrep 'warning |error |"[$$]ERROR" |warning_file |error_file |warning_modify_input |error_modify_input ' $(OGR2) | \
	    sed -e 's/"[$$]ERROR"/either/' \
		-e 's/_modify_input[[:space:]]*<[[:space:]]*[^[:space:]]*[[:space:]]*/ /' \
		-e 's/_file "[^"]*"/ /' \
		-e 's/either "[^"]*"/either /' \
		-e 's/_modify_input / /' \
		-e 's/modify_input / /' \
		-e 's/[[:space:]][[:space:]]*/ /g' \
		-e 's/^[^-]*[[:space:]]\([a-z][a-z]*\) \([a-z][a-z]*-[a-z][a-z]*\)/\1:\2/' \
		-e 's/[[:space:]].*//' | grep -v '^#*$$' | sort -u | \
	    sed -e 's/:/ /' -e 's/^/* /' > "$$TMP1"; \
	grep '^[*]' $(MESSAGES) | sort > "$$TMP2"; \
	comm -23 "$$TMP1" "$$TMP2"> "$$TMP3"; \
	comm -13 "$$TMP1" "$$TMP2" > "$$TMP4"; \
	if [ -s "$$TMP3" ]; then \
		echo "ERROR: In ogr2, but not in $(MESSAGES)"; \
		sed 's/^/    /' < "$$TMP3"; \
	fi; \
	if [ -s "$$TMP4" ]; then \
		echo "ERROR: Not in ogr2, but in $(MESSAGES)"; \
		sed 's/^/    /' < "$$TMP4"; \
	fi; \
	[ ! -s "$$TMP3" ] && [ ! -s "$$TMP4" ] && echo "No problems with messages"; \
	[ ! -s "$$TMP3" ] && [ ! -s "$$TMP4" ]
