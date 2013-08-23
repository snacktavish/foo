Git Introduction
================

MTH notes on talk for software carpentry

Tell git that you have a directory that should be "versioned"

    $ git init
 
 If you are one a computer account that has not used git before
 then you should probably introduce yourself to git. This information
 will be associated with the changes that you make to files later:

    $ git config --global user.name "Mark T. Holder"
    $ git config --global user.email "mtholder@gmail.com"

Because working with git will involve making notes about your changes
you should probably go ahead and register your text editor with git.
The following lines tell git how to call your text editor in a way that 
will pause git until you close your editing session. The commands differ
a bit from editor to editor.  

On Windows using Notepad++ in the standard
location for a 64-bit machine, you would use:

    $ git config --global core.editor "'C:/Program Files (x86)/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"

(thanks to
http://stackoverflow.com/questions/1634161/how-do-i-use-notepad-or-other-with-msysgit/2486342#2486342 
for that tip)

On Mac, with TextWrangler if you installed TextWrangler's command line tools
then you should have an "edit" command. So you can use the git command:

    $  git config --global core.editor "edit -w"


Let's make a change, save it to our file system, and then save it into git.

Open a file "dummy.txt" and add a few lines of any text to it.  

    $ echo 'hi, there' > dummy.txt
    $ echo 'this is a second line' >> dummy.txt
    $ echo 'note that I have to use >> to append to a file so that I do not overwrite previous content' >> dummy.txt

You save the file to your filesystem in the normal way. To tell git that 
you have made some changes that you would like to have versioned in the 
repository, you have to "add" the set of changes to git:

First note that if you do *not* add the file, then git lists the file
as an "untracked" file:

    $ git status

git status is very useful. It let's you see how git views the current
state of the directory that correspond to your repository.
 

To add a set of changes run:

    $ git add dummy.txt
 

Now if you repeat the status check:

    $ git status

you should see the new "dummy.txt" file are listed as the "Changes to be 
committed." Often when we want to flag a set of changes as constituting
a version of our software. That is why there are two steps involved in saving
a set of change. We have done the first step. Adding a file puts it on the 
"stage". To complete the transaction and make a new version of our repository
we can simply run:

   $ git commit

This will drop you enter your text editor so that you can write a message
explaining what you have done.

If you did not correctly configure your favorite text editor (see the steps
above about using the git config commands), then you'll probably 
be dropped into vim which is a great UNIX text editor. If you want to learn 
vim you should do that when you are not in the process of learning git. To exit 
vim simply type :q! then return (that is a colon, a lowercase q, and exclamation 
point and a return).

Commit messages are very important for telling others (and reminding yourself)
what you were trying to accomplish with a set of changes. The "Best Practices"
for a commit message are:
  * a short (<72 chars) summary.
  * a blank line
  * a description of the commit wrapped such that no line is longer than 72 characters.

When you are done writing the message, you can save and close it. On some
editors, you have to quit the editor session to return control to git.

If the commit message was received then the commit should succeed. You should see
a message with something like:

    [master some cryptic string that matches the regex [0-9a-d]+ here]
    1 file changed, 3 insertions(+)
    create mode 100644 dummy.txt

To let you know that you've added changes to one file.

Understanding git
=================
What just happened? Your working directory has the current code (just a dummy.txt file
with whatever text you put in it).  Think of this as a snapshot of your code.  Git
stores snapshots.  Everytime you commit you are adding a new snapshot to git's 
repository. Git's repository is just a database of snapshots and information about
the ancestry of each snapshot. In addition to looking at the files currently stored
in your directory and previous commits stored in the repository, git also keeps track
of an intermediate staging area (often referred to as the index in git documentation).

"git add" copies file contents from your working directory into the staging area. Git
commit copies file contents from the staging area into the repository and stores the
necessary information about the commit. In particular, it keeps track of the parent of 
the commit and your commit message.  The parent is simply the name of a previously 
committed snapshot. In git documentaion, you'll often see people refer to the "HEAD."
The HEAD is the identifier for the snapshot that will be the parent of you next
commit. Try running:

    $ echo 'a new last line' >> dummy.txt
    $ git status
    $ git diff
    $ git add dummy.txt
    $ git status
    $ git commit -m 'Added a silly new last line'
    $ git status
    $ git log
 
Does the output make sense? There are two new commands here:

  * diff shows you the difference between your working directory and the snapshot in the staging area.
  * log shows you the commit messages for all of snapshots that were stored 
      in the history of the current HEAD.
 
We don't see "parent" snapshot in the output, but git stores it. It is able
to present the full history of your work because it traverses the snaphsots
from the current HEAD back until the intial point at which you added the
directory to version control (the initial commit has no parent).

Seeing the history of notes that we've made in commit messages is cute, but
not terribly useful. Fortunately, we can navigate the ancestry graph to 
restore any snapshot that we are interested in.  The command for this is 
git checkout, and it takes an argument: the name of the snapshot that you
want to restore. First you need to figure out the "name" of the commit that you want to restore. Look at the output of
git log.  For each commit there are four pieces of information:
  * the commit's SHA-1, a nasty-looking string of 40 characters. This is a name for the commit;
  * the committer's name (this is coming from the git config stuff that you did earlier);
  * the time of "git commit" that created the snapshot; and
  * the first line of the commit's message.


In my output, I see:

commit a0208ab9763f6fba0e7788127cbd7c494d335750
Author: Mark T. Holder <mtholder@gmail.com>
Date:   Wed Aug 14 12:10:16 2013 -0500

    Initial commit

If I then run:

    $ git checkout a020

then git will "checkout" that initial commit. Note:

  1. I
can abbreviate the long SHA-1 commit "name" rather than typing the whole thing.
  2. You're SHA-1 will be different from mine, so the argument that you give to git checkout will have to differ.

What does "git checkout" do? It does two things:
  1. For every file that has been added the the current git HEAD, it edits that file in your working directory such that it is identical to it's state at the point of the old commit which you are "checking out." This means that some files may be deleted from your working directory.

  2. Second (and this is kind of hidden from view inside the bowels of git) it sets the HEAD to point to the commit that you are checking out.

Basically you have just restore the state of your directory to what it was at the time of the previous commit.  So git allows you to store many versions of a set of file. It is 
similar to the "track changes" feature in word processing
software except that it is working across an entire set of
files.

So how do we get back to the version of all of our changes to dummy.txt?  We just checkout the last commit.  If you scroll up in your terminal's history you might find the SHA-1 from the previous git log command. You could use that. But
 you may have forgotten to make note of it.

 The more general way is to ask git what the current branches exist in the entire set of snapshot histories.
You can do that with:

   $ git branch

You should see something like:

* (no branch)
  master

The * indicates where your HEAD is at. A few steps ago, we
checkout an old commit. We just used its raw, SHA-1 name, and that commit was never designated as a major branch of 
development in our software project.  So it is not on a branch of its own. That is what the "(no branch)" means.

The default branch in git is master. When we were making commits before we were updating the master branch.  Technically speaking when you "git commit" you:

  1. add the staged snapshot of file to the repository.
  2. create a SHA-1 name for this new commit.
  3. note the current HEAD as the parent of this new
    commit.
  3. reset the value of HEAD so that it now refers to the new commit.
  4. advance your current branch so that it now refers to the new commit.

I did not mention this before, but every commit we were doing was advancing the "master" pointer. We were adding
changes along the master branch, and the bookkeeping for 
that is nothing more than git keeping track of the fact that
"master" now points to a more recent commit. 

We don't usually use the SHA-1 when we want to checkout a commit. Usually we just ask for the name of the branch. So:

    $ git checkout master

will return us to where we were before.