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
* Run `onap-gerrit-review2 -l -s` along with the name of the
temporary directory where the code was cloned

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
* Run `onap-gerrit-review2 -l -s` along with the name of the
temporary directory where the code was cloned


### `onap-gerrit-review2`

`onap-gerrit-review2` can be re-run as needed.

``` shell
onap-gerrit-review2 [options] /path/to/temporary/directory
```

* If run with the `-l` (limit) option, it limits error checking to
the directories and files that were modified by the Gerrit update.
The default without `-l` is to run checks against all files in
the repository.
* If run with the `-s` (silent) option, it limits the output to errors
that were found.
The default without `-s` is to also print messages about successful
checks that passed and some other minor information.

## Things tested for:

The following checks are included.
Some checks are limited to run only on the files that have been
modified in the Gerrit pull request.

* Copyright $YEAR found in all source files
* ALL files have a NL ending
* No files have TODO in them
* DIR/pom.xml version $version has not been released
* Found $version in changelog.md file
* Did not find ambiguous dates in changelog.md file
* The version numbers in changelog.md file are in the correct order
* $dir/pom.xml versions match in $dir/version.properties: $vp
* No $dir/version.properties file needed
* $dir/pom.xml versions match in $dir/setup.py: version $sp
* No 'import ... *' found in (updated) java files
* JSON files all have valid JSON in them
* YAML files all have valid YAML in them
* Found a ChangeLog.md file in the updated files
* Found Issue-ID: in the git log commit: $ISSUEID
* Found Issue-ID: $issueid in the changed ChangeLog.md files


## To install:

The expectation is that the `onap-gerrit-review` tools will all
be found in your `PATH` variable.
* Add the `onap-gerrit-review/bin` directory to your `PATH` variable.
* OR copy (or symlink) the files from `bin` into a directory already in your `PATH`.
* OR create a new `bin` directory for the `onap-gerrit-review` tools, and
add that `bin` directory to your `PATH` variable.

## Secondary tools:

In addition to `onap-gerrit-review` and `onap-gerrit-review2`, the following
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

### `onap-gerrit-review-extract-xml`

`onap-gerrit-review-extract-xml` is used to extract information from XML
files. In particular, it is used to extract information from the pom.xml
files used by ONAP.
