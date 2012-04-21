--- 
layout: post
title: "Editing the diff in Interactive Git Add"
---

# {{ page.title }}

This is a followup post to <a href='http://jackdempsey.me/2010/07/29/git-tips-add-content-intelligently.html'>Git tip add content intelligently</a>.

Being able to use git add -i and split out hunks to add is certainly
useful. At times though, the content you want to split lays across
consecutive lines of code like this:

<img src='/images/git-add-i/1.png' />

And you're trying to select only the last too. So, hit 'e' to edit this
diff:

<img src='/images/git-add-i/2.png' />

You'll see some instructions inline. The first time I tried this I was a
bit confused by the "make them ' ' lines". Single blank space lines? I
quickly realized they meant to remove the '-' at the begining of the
lines. 

To not include a line that has a '+' at the start, simply delete it:

<img src='/images/git-add-i/3.png' />

So now we have a diff that says "add these two lines" which is what we
want. Save that file and quit your editor.

<img src='/images/git-add-i/4.png' />

You'll see at the bottom there that the same file is listed in both
'Changes to be committed' and 'Changed but not updated'. This is because
you've selected only some of the file. When you think about adding
things in git as adding content and not files, this is a little easier
to understand.

<img src='/images/git-add-i/5.png' />

I run gst (git status) and gd (git diff) to show you that what is
currently not staged is that line we removed in the interactive edit.

<img src='/images/git-add-i/6.png' />

And a gdc (git diff --cached) shows us what's in the staging area--the
two lines we added before.

<img src='/images/git-add-i/7.png' />

So here I'll show how to handle when you have some additions and
subtractions near each other. 

<img src='/images/git-add-i/8.png' />

<img src='/images/git-add-i/9.png' />

We edit the diff again and this time just delete the '-' at the start of
the line. This let's git see this line as unchanging, and a reference
point to use in determining just where to place the '+' line you're
about to add. 

<img src='/images/git-add-i/10.png' />

Again we're left with some content staged, and some not. 


I might record a video of this as it's likely easier to follow that way,
but I wanted to get this down first to provide some structure.

Git is powerful but takes some practice. I'd recommend trying this out
a few times on a test repo before doing it on a production clone.
