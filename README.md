= hub

== Description

'hub' is a ridiculously tiny script that's basically an alias for:

`git clone git@github.com:#{user}/#{repo}.git`.

The nice-ness is in the usage - way less typing, and it assumes I mean
one of my repositories when there's no '/' in the name, otherwise it
assumes I want to clone somebody else's repository.

Also made an elaborate installer around this tiny tool that I intend to convert to a gem
that I can use as an installer across all my projects.

== Install

1. `rake install`
2. Follow the prompts

If you come across a bug while installing, do let me know. Better yet, find a fix
for it and make a pull request.

== Usage

I'll just paste some sample terminal output here:
```
  ~/Code: hub dotfiles
  Assuming you mean 'vyder/dotfiles'.
  Cloning into 'dotfiles'...
  remote: Counting objects: 6, done.
  remote: Compressing objects: 100% (4/4), done.
  remote: Total 6 (delta 0), reused 6 (delta 0)
  Receiving objects: 100% (6/6), done.
  Checking connectivity... done.
  ~/Code: hub defunkt/dotjs
  Assuming you mean 'defunkt/dotjs'.
  Cloning into 'dotjs'...
  remote: Counting objects: 426, done.
  remote: Total 426 (delta 0), reused 0 (delta 0)
  Receiving objects: 100% (426/426), 535.84 KiB | 974.00 KiB/s, done.
  Resolving deltas: 100% (181/181), done.
  Checking connectivity... done.
  ~/Code: hub asdfjhalsdf
  Assuming you mean 'vyder/asdfjhalsdf'.
  Cloning into 'asdfjhalsdf'...
  ERROR: Repository not found.
  fatal: Could not read from remote repository.

  Please make sure you have the correct access rights
  and the repository exists.
```

== Uninstall

1. cd into the repository
2. `rake uninstall`

== Future work / TODO:

1. Let user install to a custom path
2. Create a loop from verifying ID to getting a new ID
3. Find a way to test the installer
