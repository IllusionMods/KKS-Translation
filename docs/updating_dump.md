# Updating the dump

How to update the dump either TextDump is updated or patch/update/expansion is released. Steps will be documented using the git command line tools. It assumes you have a git remote named `IllusionMods` pointing to this repo, and you can push directly to it.  If possible clear any backlog of PRs before doing this process to make everyones life easier.

## Prepare local workspace

Either make a new local clone of this repo or delete any `clean-dump` branch in your local workspace.

Ensure things are up to date:

```
$ git fetch --all
```

Check out latest `clean-dump` branch:

```
$ git checkout -b clean-dump --track IllusionMods/clean-dump
```

## Updating `clean-dump` branch

### Run TextDump

The easiest way is to move the two `XUnity*` folders out of `BepInEx/plugins` (I usually just drag them up to the `BepInEx` folder temporarily) and install the `TextDump` plugin. Start the game, monitor the log until TextDump says the dump is complete. If you had `TextDump` disabled in the config, enable it, quit the game, and let it dump again to ensure things happen at the correct point during startup.

### Merge the clean dump

The easiest way to do this is using some sort of diff tool, configured to ignore lines matching the following regexs (If using WinMerge add them under: Tools -> Filters -> Linefilters)

```
^// Dumped for .* 
^//$
```

Compare `[Game Root]\TextDump` with `[workspace]\Translation\en`.

The majority of the time there will be no diffs to existing files, and only new files added, use your diff tool to copy those left to right. If any existing files have diffs, compare them. If only new lines are added, close the comparison and copy left to right at the file level.

#### Dealing with changed or removed lines

If any lines have been changed or removed (typos fixed, rare), add a new file named `translation_oldassets.txt` and copy the removed/changed lines from the existing dump (right) into that file (this is for backwards compatibility with older installs as well as for not losing entries that may come back in a later patch).  Once that's done overwrite the whole file, left to right as above.

### Pushing the Clean Dump changes

#### Adding new/updated files
Add any new directories/files to git (use `git status` to list them).

It's usually safe to add `Translation/en/RedirectedResources` outright.

```
$ git add Translation/en/RedirectedResources
```

Any new files in text probably should be added one at a time if you're not using a newly cloned workspace.

#### Commit the changes

Check a newly dumped file to get the versions for the commit messages, then commit.

```
$ git commit -a -m "Clean dump: KKS vX.Y.Z, TextDump vX.Y.Z"
```

#### Push `clean-dump` branch

```
$ git push IllusionMods clean-dump
```

## Updating `master` branch

When updating the master branch from a new dump we push directly without a PR, so it's important that the merge it done properly and no other changes are contained in your workspace.

### checkout new branch

```
$ git fetch --all
$ git checkout -b update-dump-[date] --track IllusionMods/master
```

### merge `clean-dump` branch in

NOTE: you **must not** squash merge during this part of the process or future updates become a nightmare.

```
$ git merge --no-squash clean-dump
```

Usually this will be clean, without conflicts. If there are conflicts you need to resolve them and then run:

```
$ git merge --no-squash --continue
```

### review changes before pushing

Since you have to push the merge directly without using a PR, you need to manually compare and make sure things look right:

```
$ git diff IllusionMods/master
```

Fix anything that needs fixing (hopefully nothing).  If any previously translated lines have been removed, make sure to add the removed translations to the `translation_oldassets.txt` files and commit those.

### push to master

```
$ git push IllusionMods [your update branch name]:master
```

## Next steps

At this point things go back to the normal PR flow.  A PR to sync the new files with the existing translations should be filed ASAP.











