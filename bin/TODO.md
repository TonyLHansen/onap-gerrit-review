TODO

# onap-gerrit-review-step2

==== shellcheck SC2035 ====
Consider using find ./* instead of * so names with dashes won't become options. [SC2035]
It would also allow catching files such as .gitignore, .pylintrc, etc. that are currently ignored.
But if so, need to get rid of .git, and filenames look nicer without the leading './'

Get rid of .class, .pyc, /.tox/, __pycache__ or .pytest_cache files?
Add complaint about any .class,.pyc,.tox,etc that might have been put into gerrit

add support for .c/.h/.cpp/.cc files

if we have ci-manage info available, can better check for version.properties, pom.xml, etc requirements

Check all of the new files that have copyrights. Make sure that the same company exists in all of them.


# onap-gerrit-review-verify-releasefile:

Verify the values in project: and log_dir: against pom.xml project/name.

It's possible for the release file to have multiple containers:name+version blocks
For example, look at platform/genprocessor/pom.xml and releases/...-genprocessor-container.yaml.

In find_matching_pom_file(), need to match against pom.xml build/plugins/plugin/configuration/images/image/name
