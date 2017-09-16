I realize that ChangeLogs are the epitome of pointlessness.  Nevertheless, many projects use them and, for whatever reason, are reluctant to give them up.  So, I wrote some scripts to help ease the pain of managing these wretched files with git.

If you're a project maintainer and you use the existence of these scripts to justify the continued existence of ChangeLog files, then I hope something bad happens to you, like you're forced to write in PHP and COBOL for all eternity, meticulously typing out "Likewise" in millions of ChangeLog entries by hand.

Using these scripts:

* `git clone`, of couse!
* Install the git ChangeLog merge driver, following [these instructions](https://gnu.wildebeest.org/blog/mjw/2012/03/16/automagically-merging-changelog-files-with-mercurial-or-git/)
* Optionally, install a skeleton ChangeLog generator, like `generate-changelog` from [the gcc refactoring repository](https://github.com/davidmalcolm/gcc-refactoring-scripts), or `mklog` from [gcc's contrib](https://github.com/mirrors/gcc/blob/master/contrib/mklog)

Put the `git-*` scripts from this repository into your `PATH`.  This will let git find them.

In your ChangeLog-using project, use the provided `prepare-commit-msg` in your hooks, like so:

    cd $project/.git/hooks
    ln -s $git_gnu_changelog/prepare-commit-msg

Finally, define a "changelog" diff driver.  This is used by `git gdb-email` (see below).  To do this, add this to your `~/.gitconfig`:

```
[diff "changelog"]
	name = GNU-style ChangeLog diff driver
	command = /bin/true
```

Now, when developing, write ChangeLog entries as usual, in the appropriate files in the tree.  When you commit, the ChangeLog entries will be automatically put into the commit message.  This works well with rebasing and editing commits as well -- the script will replace the old ChangeLog entries in the commit message with the updated ones from the commit itself.

The ChangeLog merge script makes sure that merges and rebases will go smoothly.  You won't see conflicts arising from the ChangeLog files.

If you want to fix up the date on a ChangeLog entry, you can use `git simple-fixup`.  It examines the most recent commit and rewrites the dates on all the modified ChangeLog files.  You can then `git commit --amend` to update the commit; the commit message hook will automatically update the date in the commit message

When you want to submit your series, you'll probably want to fix up the dates on all the ChangeLog entries.  You can use `git fixup-changelogs` to rewrite the dates in the files and the commits for a series of commits.

Finally, when submitting a patch series, many GNU projects do not want the ChangeLog files to appear in the diffs.  For projects like this, use `git gdb-email`.  It works just like `git send-email`, but it strips ChangeLog files from the email -- leaving just the copies in the commit messages themselves.
