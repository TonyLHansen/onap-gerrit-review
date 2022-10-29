TODO

# onap-gerrit-review-xml

for pom.xml files, look for stray strings in the middle of non-leaf nodes:
    <properties> bad-string
        <propname>good-string</propname>
    </properties>
    complain about bad-string


# onap-gerrit-review-step2

only look for copyright and creative commons attributions in the top N lines (N TBD) instead of the entire files

DONE check on extension casing
DONE CC license check on rst files

support a comment to the commit log message, such as:
No-Functional-Changes
and that would override ogr on several error conditions, such as these:
ERROR: (missing-package-json) No ./package.json
ERROR: (changelog-or-release-file-missing) Did not find either a ChangeLog.md or a releases file in the updated files

Decide what to do with these files as far as copyright:
*.conf
*.config files
*.properties
README.md files
sh-template
tox.ini
version.properties

verify that *-template files have {{ }} pairs in them
verify the template syntax?

add some specific tests on $TOPDIR/pom.xml

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


shell scripts
optionally run shellcheck


# onap-gerrit-review-verify-releasefile:

Verify the values in project: and log_dir: against pom.xml project/name.

It's possible for the release file to have multiple containers:name+version blocks
For example, look at platform/genprocessor/pom.xml and releases/...-genprocessor-container.yaml.

In find_matching_pom_file(), need to match against pom.xml build/plugins/plugin/configuration/images/image/name
