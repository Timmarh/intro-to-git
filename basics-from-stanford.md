# Chapter 2. Basic Tricks

Rather than diving into a sea of Git commands, use these elementary examples to get your feet wet. Despite their simplicity, each of them are useful. Indeed, in my first months with Git I never ventured beyond the material in this chapter.

## Saving State
About to attempt something drastic? Before you do, take a snapshot of all files in the current directory with:

```
$ git init
$ git add .
$ git commit -m "My first backup"
```

Now if your new edits go awry, restore the pristine version:

`$ git reset --hard`
To save the state again:

`$ git commit -a -m "Another backup" `

## Add, Delete, Rename
The above only keeps track of the files that were present when you first ran **git add**. If you add new files or subdirectories, you’ll have to tell Git:

`$ git add readme.txt Documentation`
Similarly, if you want Git to forget about certain files:

```
$ git rm kludge.h obsolete.c
$ git rm -r incriminating/evidence/
```

Git deletes these files for you if you haven’t already.

Renaming a file is the same as removing the old name and adding the new name. There’s also the shortcut **git mv** which has the same syntax as the **mv** command. For example:

`$ git mv bug.c feature.c` 

## Advanced Undo/Redo
Sometimes you just want to go back and forget about every change past a certain point because they’re all wrong. Then:

`$ git log` 

shows you a list of recent commits, and their SHA1 hashes:

    commit 766f9881690d240ba334153047649b8b8f11c664
    Author: Bob <bob@example.com>
    Date:   Tue Mar 14 01:59:26 2000 -0800
    
        Replace printf() with write().
    
    commit 82f5ea346a2e651544956a8653c0f58dc151275c
    Author: Alice <alice@example.com>
    Date:   Thu Jan 1 00:00:00 1970 +0000
    
        Initial commit.

The first few characters of the hash are enough to specify the commit; alternatively, copy and paste the entire hash. Type:

`$ git reset --hard 766f`
to restore the state to a given commit and erase all newer commits from the record permanently.

Other times you want to hop to an old state briefly. In this case, type:

`$ git checkout 82f5`

This takes you back in time, while preserving newer commits. However, like time travel in a science-fiction movie, if you now edit and commit, you will be in an alternate reality, because your actions are different to what they were the first time around.

This alternate reality is called a branch, and[ we’ll have more to say about this later](http://www-cs-students.stanford.edu/%7Eblynn/gitmagic/ch04.html#branch). For now, just remember that

`$ git checkout master`
will take you back to the present. Also, to stop Git complaining, always commit or reset your changes before running checkout.

To take the computer game analogy again:

**git reset --hard**: load an old save and delete all saved games newer than the one just loaded.
**git checkout**: load an old game, but if you play on, the game state will deviate from the newer saves you made the first time around. Any saved games you make now will end up in a separate branch representing the alternate reality you have entered. [We deal with this later](http://www-cs-students.stanford.edu/%7Eblynn/gitmagic/ch04.html#branch).

You can choose only to restore particular files and subdirectories by appending them after the command:

`$ git checkout 82f5 some.file another.file`

Take care, as this form of **checkout** can silently overwrite files. To prevent accidents, commit before running any checkout command, especially when first learning Git. In general, whenever you feel unsure about any operation, Git command or not, first run **git commit -a**.

Don’t like cutting and pasting hashes? Then use:

`$ git checkout :/"My first b"`
to jump to the commit that starts with a given message. You can also ask for the 5th-last saved state:

`$ git checkout master~5`

## Reverting
In a court of law, events can be stricken from the record. Likewise, you can pick specific commits to undo.

```
$ git commit -a
$ git revert 1b6d
```

will undo just the commit with the given hash. The revert is recorded as a new commit, which you can confirm by running **git log**.

## Changelog Generation
Some projects require a [changelog](http://en.wikipedia.org/wiki/Changelog). Generate one by typing:

`$ git log > ChangeLog`

## Downloading Files
Get a copy of a project managed with Git by typing:

`$ git clone git://server/path/to/files` 
For example, to get all the files I used to create this site:

`$ git clone git://git.or.cz/gitmagic.git`
We’ll have much to say about the **clone** command soon.

## The Bleeding Edge
If you’ve already downloaded a copy of a project using git clone, you can upgrade to the latest version with:

`$ git pull`

## Instant Publishing
Suppose you’ve written a script you’d like to share with others. You could just tell them to download from your computer, but if they do so while you’re improving the script or making experimental changes, they could wind up in trouble. Of course, this is why release cycles exist. Developers may work on a project frequently, but they only make the code available when they feel it is presentable.

To do this with Git, in the directory where your script resides:

```
$ git init
$ git add .
$ git commit -m "First release"
```

Then tell your users to run:

`$ git clone your.computer:/path/to/script`

to download your script. This assumes they have ssh access. If not, run **git daemon** and tell your users to instead run:

`$ git clone git://your.computer/path/to/script`

From now on, every time your script is ready for release, execute:

`$ git commit -a -m "Next release"`
and your users can upgrade their version by changing to the directory containing your script and typing:

`$ git pull`
Your users will never end up with a version of your script you don’t want them to see.

## What Have I Done?
Find out what changes you’ve made since the last commit with:

`$ git diff`
Or since yesterday:

`$ git diff "@{yesterday}"`
Or between a particular version and 2 versions ago:

`$ git diff 1b6d "master~2"`
In each case the output is a patch that can be applied with git apply. Try also:

`$ git whatchanged --since="2 weeks ago"`

Often I’ll browse history with [qgit](http://sourceforge.net/projects/qgit) instead, due to its slick photogenic interface, or [tig](http://jonas.nitro.dk/tig/), a text-mode interface that works well over slow connections. Alternatively, install a web server, run `git instaweb` and fire up any web browser.

## Exercise
Let A, B, C, D be four successive commits where B is the same as A except some files have been removed. We want to add the files back at D. How can we do this?

There are at least three solutions. Assuming we are at D:

1. The difference between A and B are the removed files. We can create a patch representing this difference and apply it:   
   `$ git diff B A | git apply`

2. Since we saved the files back at A, we can retrieve them:    
   `$ git checkout A foo.c bar.h`
   
3. We can view going from A to B as a change we want to undo:   
   `$ git revert B`
   
Which choice is best? Whichever you prefer most. It is easy to get what you want with Git, and often there are many ways to get it.