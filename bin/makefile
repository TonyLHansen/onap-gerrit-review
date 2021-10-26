SHSRC=	onap-gerrit-review onap-gerrit-review2
PYSRC=	onap-gerrit-review-verify-eol \
	onap-gerrit-review-verify-json \
	onap-gerrit-review-verify-yaml \
	onap-gerrit-review-extract-xml

none:
	@make tests - run run-shellcheck and run-pylint
	@make run-shellcheck
	@make run-pylint

tests: run-shellcheck run-pylint

run-shellcheck:
	shellcheck -f gcc $(SHSRC) | sed 's/ note:/ warning: note:/'

run-pylint:
	for i in $(PYSRC); do pylint $$i; done
