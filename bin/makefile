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
	@make tests - run run-shellcheck and run-pylint
	@make run-shellcheck
	@make run-pylint

tests: run-black run-shellcheck run-pylint messages

run-black:
	black -l120 $(PYSRC)

run-shellcheck:
	shellcheck -f gcc $(SHSRC) | sed 's/ note:/ warning: note:/'

run-pylint:
	for i in $(PYSRC); do pylint $$i; done

MESSAGES=../warnings-and-errors.md
OGR2=onap-gerrit-review-step2

messages:
	@TMP1=$$(mktemp); TMP2=$$(mktemp); \
	TMP3=$$(mktemp); TMP4=$$(mktemp); \
	trap 'rm -f "$$TMP1" "$$TMP2" "$$TMP3" "$$TMP4"' 0 1 2 3 15; \
	egrep 'warning |error |"[$$]ERROR" ' $(OGR2) | \
	    sed -e 's/"[$$]ERROR"/either/' \
		-e 's/^[^-]*[[:space:]]\([a-z][a-z]*\) \([a-z][a-z]*-[a-z][a-z]*\)/\1:\2/' \
		-e 's/[[:space:]].*//' | grep -v '^$$' | sort -u | \
	    sed -e 's/:/ /' -e 's/^/* /' > "$$TMP1"; \
	grep '^[*]' $(MESSAGES) | sort > "$$TMP2"; \
	echo In ogr2 -- not in $(MESSAGES); \
	comm -23 "$$TMP1" "$$TMP2" | tee "$$TMP3"; \
	echo Not in ogr2 -- in $(MESSAGES); \
	comm -13 "$$TMP1" "$$TMP2" | tee "$$TMP4"; \
	[ ! -s "$$TMP3" ] && [ ! -s "$$TMP4" ]
