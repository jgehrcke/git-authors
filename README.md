A micro Python script for listing the authors in a git repository sorted by
the time of their first contribution. Traits:


* Properly deals with the byte stream emitted by `git log`.
* Supports Python 2.7 and 3.4.
* Supports Linux and Windows.


## Usage

```
$ git log --encoding=utf-8 --full-history --reverse "--format=format:%at;%an;%ae" | \
    git-authors > authors-by-first-contrib.txt
```

`git-authors` writes a formatted list of authors to stdout. The data is
UTF-8-encoded. Lines are separated by a single `\n` character.


## Resource consumption

This script implements on-the-fly stream processing and uses an efficient
data structure for keeping track of authors. It therefore requires almost
no memory and just a little amount of CPU power.

The resource requirements for analyzing a repository are therefore
predominantly dictated by whatever `git log` consumes: it performs
reasonably fast even for large repositories: with git version 2.1.4,
analysis of the Linux repository (with more than 500.000 commits)
takes about 7 seconds on my medium-equipped test machine.


## Notes

* It is not uncommon for an author to use different email addresses over
  time. Hence, authors are distinguished by their name only (specifically, by
  the git data field `%an`). Clearly, the resulting output might need higher
  intelligence for meaningful interpretation, such as for detecting duplicates.

* The contribution date is extracted from the git data field `%at`, also
  known as the "author date". See http://stackoverflow.com/a/11857467/145400.

* The output of `git log` can contain byte sequences that are invalid in
  the UTF-8 codec. These get replaced with a question mark in the output.
  This is unrelated to the `--encoding=utf-8` option (note: it seems that
  the `--encoding` argument to `git log` has been removed in quite recent
  git releases. Remove it from the command line if you get a corresponding
  error). For details about how git internally handles text encoding, see
  http://git-scm.com/docs/git-log#_discussion.

* If there appears to be an author with a semicolon in his/her name, you'll
  manage to use a different separator.

