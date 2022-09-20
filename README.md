# onap-gerrit-review
# Run various tests against an ONAP Gerrit review, checking for various inconsistencies and issues.

These programs were designed to be used by a committer on the ONAP
(www.onap.org) open source project, when reviewing a Gerrit pull request.
The tools were designed to be executed on a Linux system, but may work
under other operating systems.

## Usage:

### `onap-gerrit-review`

#### Method 1

To use `onap-gerrit-review`:

* Someone submits a Gerrit pull request for review.
* Open the Gerrit pull request in a browser.
* Click the vertical "`...`" in the upper right corner,
select "Download patch", and click the double-rectangle
to the right side of the "Pull" command that is displayed
there. (Alternatively, copy and paste the entire "`git pull`" string.)
* Run the `onap-gerrit-review` command with the string copied above
as the arguments, as in:

``` shell
onap-gerrit-review git pull "https://YourID@gerrit.onap.org/..."
```

`onap-gerrit-review` will:

* Do a clone of the original code into a temporary directory.
* Capture information.
* Do a `git pull` of the update on top on the cloned code.
* Capture more information.
* Print the name of the temporary directory that is being used.
* Run `onap-gerrit-review-step2 -l -s` along with the name of the
temporary directory where the code was cloned

Normally `onap-gerrit-review` will do its clone from the `master` branch.
If your pull request is against a different branch, then use the `-b branch` option to override that.

#### Method 2

You may also use `onap-gerrit-review` with a local cloned copy of a
Gerrit repository before it is pushed to Gerrit.
Instead of passing the `git pull` parameters, you pass in the directory
path where the code has been cloned:

``` shell
onap-gerrit-review /path/to/code
```

`onap-gerrit-review` will:

* Do a clone of the original code into a temporary directory.
* Capture information.
* Copy in your updates on top on the cloned code.
* Capture more information.
* Print the name of the temporary directory that is being used.
* Run `onap-gerrit-review-step2 -l -s` along with the name of the
temporary directory where the code was cloned
* If the '-c' option is set, any .tox and .pytest_cache directories will be cleaned out before scanning.

#### Method 3

`onap-gerrit-review` was also designed to be used within a gerrit pipeline.
If the `-G` option is specified, the project, refspec, url, and branch
will be extracted from the corresponding GERRIT_ variables (e.g., GERRIT_PROJECT).
`onap-gerrit-review` will then act as if it had been invoked as:

``` shell
onap-gerrit-review -b "$GERRIT_BRANCH" git pull "$GERRIT_URL/$GERRIT_PROJECT" "$GERRIT_REFSPEC"
```

#### Environment variables:

The following environment variables may be set to run additional checks.

* $ONAP_GERRIT_REVIEW_PYLINTRC If set, run 'pylint' with PYLINTRC set to this value.
* $ONAP_GERRIT_REVIEW_PYDOCSTYLE If set, run 'pydocstyle'. Must be one of pep257, numpy or google.
* $ONAP_GERRIT_REVIEW_BLACK If set, run 'black --line-length 120' on the python files.
* $ONAP_GERRIT_REVIEW_JSLINTERS If set to 'jshint', run it on the javascript files.
* $ONAP_GERRIT_REVIEW_DOCKERLINTER If set, run dockerlinter on the Dockerfile files.
* $ONAP_GERRIT_REVIEW_INFO_YAML_SCHEMA If set, run schema verification for INFO.yaml files.


### `onap-gerrit-review-step2`

`onap-gerrit-review-step2` can be re-run as needed.

``` shell
onap-gerrit-review-step2 [options] /path/to/temporary/directory
```

* If run with the `-l` (limit) option, it limits error checking to
the directories and files that were modified by the Gerrit update.
The default without `-l` is to run checks against all files in
the repository.
* If run with the `-s` (silent) option, it limits the output to errors
that were found.
The default without `-s` is to also print messages about successful
checks that passed and some other minor information.

#### git commit log temporary message suppression

Each error and warning message has a code associated with it printed in parentheses
after the word `ERROR:` or `WARNING:`.

The error and warning messages may be turned off (ignored) temporarily by listing the codes (with a hyphen in front)
in the commit message, preceded by "onap-gerrit-review:", as in
"`onap-gerrit-review: -no-top-level-license-txt-file`".
That error message and any associated additional information will not be printed;
nor will the error cause the program to exit with an error code.

A more extended example is:
```
commit 96bb1937750e59e028ae9d06c3689183b2e014a2
Author: Someone <someone@company.com>
Date:   Tue Apr 28 19:38:41 2022 +0000

    Readme updated to include something
    onap-gerrit-review: -changelog-or-release-file-missing
    
    Change-Id: Iafac92c7fa59103cc48372882d7558b5ca466a17
    Signed-off-by: Someone <someone@company.com>
    Issue-ID: PROJECT-2974
```

#### pom.xml file permanent message suppression

Each error and warning message has a code associated with it printed in parentheses
after the word `ERROR:` or `WARNING:`.

Error and warning messages may be turned off (ignored) permanently by listing the codes (with a hyphen in front)
in a `pom.xml` file within a `properties/onap-gerrit-review` block, as in the following example:

```xml
  <properties>
    <onap-gerrit-review>
      -code1
      -code2
      -code3,-code4,-code5
    </onap-gerrit-review>
  </properties>
```  

As shown in the example, the codes may be listed on separate lines, or on a single line separated by commas.
For messages that are associated with a specific `pom.xml` file (e.g., checks for a `version.properties` file),
the `onap-gerrit-review` block can be given in that particular `pom.xml` file, or any `pom.xml` in the
directories above up to the top level `pom.xml` of the repo.

NOTE: The lowest-most `pom.xml` file should be used for a particular message. For example,
`changelog-missing` should NEVER be suppressed from the top-most `pom.xml`, but ONLY from
lower-level `pom.xml` files.

The error messages listed, and any associated additional information, will not be printed;
nor will the error cause the program to exit with an error code.

#### $HOME/.onap-gerrit-review.rc permanent message suppression

The error and warning messages may *also* be turned off (ignored) permanently by listing the codes (with a hyphen in front)
in a `$HOME/.onap-gerrit-review.rc` file, as in
"`-no-top-level-license-txt-file`".
That error message and any associated additional information will not be printed;
nor will the error cause the program to exit with an error code.

## Things tested for:

The following checks are included.
Some checks are limited to run only on the files that have been
modified in the Gerrit pull request.
A complete list of warning and error message codes can be found in [warnings-and-errors.md].

* Copyright $YEAR found in all new source files.
* Makes sure that all new files with Copyright $YEAR have an identical string in them.
* Files have a NL ending. (Skips picture, font and various container files.)
* No files have TODO in them.
* DIR/pom.xml version $version has not been released.
* Found $version in changelog.md file.
* Did not find ambiguous dates in changelog.md file.
* The version numbers in changelog.md file are in the correct order.
* $dir/pom.xml versions match in $dir/version.properties: $vp.
* No $dir/version.properties file needed.
* $dir/pom.xml versions match in $dir/setup.py: version $sp.
* $dir/pom.xml versions match in $dir/package.json: version $sp.
* No 'import ... *' found in (updated) java files.
* JSON files all have valid JSON in them.
* YAML (.yaml and .yml) files all have valid YAML in them.
* Found a ChangeLog.md file in the updated files.
* Found Issue-ID: in the git log commit: $ISSUEID.
* Found Issue-ID: $issueid in the changed ChangeLog.md files.
* Do various tests on the files under the releases directory.
* Look for leading tabs for java and python code.
* Verify that a top-level LICENSE.txt file exists.
* Verify that LICENSE files contain a reference to Apache 2.0 license.
* For copyright files, make sure that there is also LICENSE_START and LICENSE_END lines.
* For copyright files, make sure that there is no separator (e.g. ====) line between copyright lines
* Make sure there's no text before '`Copyright`', such as words like "`Modifications`"
* Can optionally run pylint on python code, by setting $ONAP_GERRIT_REVIEW_PYLINTRC to the path of a pylint.rc file.
* Can optionally run pydocstyle on python code, by setting ONAP_GERRIT_REVIEW_PYDOCSTYLE=google. (Use `pip3 install pydocstyle` to install it. Other possible values are pep257 and numpy.)
* Can optionally run 'black --line-length 120' on python code, by setting ONAP_GERRIT_REVIEW_BLACK=yes. (Use `pip3 install black` to install it.)
* Can optionally run 'jshint' on javascript code, by setting ONAP_GERRIT_REVIEW_JSLINTERS to 'jshint'. (See "https://jshint.com/install/" for installation instructions.)
* Can optionally run 'dockerlinter' on Dockerfile files, by setting ONAP_GERRIT_REVIEW_DOCKERLINTER=yes. (Use `npm install --global dockerlinter` to install it.)
* Can optionally do schema verification on INFO.yaml files, by setting ONAP_GERRIT_REVIEW_INFO_YAML_SCHEMA to the schema file. (The schema file may be retrieved from `https://github.com/lfit/releng-global-jjb/blob/master/schema/info-schema.yaml`.)
* Each error message has a unique code names.
* $HOME/.onap-gerrit-review.rc can be used to turn off any error message, by putting "-code-name" on a line by itself in the file, following a line that says "[onap-gerrit-review]".


## To install:

The expectation is that the `onap-gerrit-review` tools will all
be found in your `PATH` variable.
* Add the `onap-gerrit-review/bin` directory to your `PATH` variable.
* OR copy (or symlink) the files from `bin` into a directory already in your `PATH`.
* OR create a new `bin` directory for the `onap-gerrit-review` tools, and
add that `bin` directory to your `PATH` variable.

## Secondary tools:

In addition to `onap-gerrit-review` and `onap-gerrit-review-step2`, the following
programs are included and will be invoked as needed. They all use python3 as
their script interpreter.

### `onap-gerrit-review-verify-eol`

`onap-gerrit-review-verify-eol` verifies that all of the files provided
end in a newline.

### `onap-gerrit-review-verify-json`

`onap-gerrit-review-verify-json` verifies that all of the files provided
can be loaded as proper JSON files using `json.load()`.

### `onap-gerrit-review-verify-yaml`

`onap-gerrit-review-verify-yaml` verifies that all of the files provided
can be loaded as proper YAML files using `yaml.safe_load_all()`.
Some YAML files in ONAP are actually Ruby templatized YAML files, whose
macros are surrounded by `{{` and `}}`.
`onap-gerrit-review-verify-yaml` takes an option `-T` that will convert
such `{{...}}`` template macros into the text `STRING` before passing the
results to `yaml.safe_load_all()`.

### `onap-gerrit-review-verify-releasefile`

`onap-gerrit-review-verify-releasefile` is used to verify the yaml files
under a releases directory.

### `onap-gerrit-review-extract-xml`

`onap-gerrit-review-extract-xml` is used to extract information from XML
files. In particular, it is used to extract information from the pom.xml
files used by ONAP.

### `onap-gerrit-review-verify-xml`

`onap-gerrit-review-verify-xml` is used to verify xml files using
`xml.etree.ElementTree.fromstring()`.
