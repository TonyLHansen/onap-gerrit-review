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

tests: run-black run-shellcheck run-pylint

run-black:
	black -l120 $(PYSRC)

run-shellcheck:
	shellcheck -f gcc $(SHSRC) | sed 's/ note:/ warning: note:/'

run-pylint:
	for i in $(PYSRC); do pylint $$i; done
