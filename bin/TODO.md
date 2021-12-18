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

Make sure that there is a LICENSE.txt file.
(Does it need to be in the top-level? Can it be named LICENSE without the .txt?)


check that there are not lines BETWEEN the copyright lines, such as ==== or ----


add a property to pom.xml and set to false explictly to indicate that version.properties files are not needed for this pom file. this will be used only by the gerrit-review-tool

shell scripts
optionally run shellcheck


# onap-gerrit-review-verify-releasefile:

Verify the values in project: and log_dir: against pom.xml project/name.

It's possible for the release file to have multiple containers:name+version blocks
For example, look at platform/genprocessor/pom.xml and releases/...-genprocessor-container.yaml.

In find_matching_pom_file(), need to match against pom.xml build/plugins/plugin/configuration/images/image/name
