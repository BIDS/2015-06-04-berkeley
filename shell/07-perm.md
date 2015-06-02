---
layout: lesson
root: ../..
title: Permissions
level: intermediate
---



Now that you've seen how the many simple commands available in the shell can be
combined into ad hoc pipelines, the incredible combinatoric
algorithmic power of the shell is at your fingertips--but only if you
have the right permissions.

> ## Permissions and Sharing {.callout}
> 
> Permissions are a subtle but important part of using and sharing files
> and using commands on Unix and Linux
> systems. This topic tends to confuse people, but the basic gist is that
> different people can be given different types of access to a given
> file, program, or computer.

At the highest level, the file system is only available to users with
a user account on that computer. Based on these permissions, some
commands allow users to connect to other computers or send files. For example:

*  `ssh [user@host]` connects to another computer.
*  `scp [file] [user@host]:path` copies files from one computer to another.

Those commands only work if the user issuing them has 'permission' to log into the
file system. Otherwise, he will not be able to access the file system at all.

Once they have accessed a computer's file system, however, different types of users may
have different types of access to the various files on that system. The
``different types of people'' are the individual __u__ser (u) who owns the file,
the __g__roup (g) who's been granted special access to it, and all
__o__thers (o). The
``different types of access'' are permission to __r__ead (r),
__w__rite to (w), or e__x__ecute (x) a file or directory.

This section will introduce three commands that allow you to manage the
permissions of your files:

*  `ls -l [file]` displays, among other things, the permissions for that file.
*  `chown [-R] [[user]][:group] target1 [[target2 ..]]` changes the individual user and group ownership of the target(s), recursively if `-R` is used and one or more targets are directories.
*  `chmod [options] mode[,mode] target1 [[target2 ...]]` changes or sets the permissions for the given target(s) to the given mode(s).

The first of these, `ls -l`, is the most fundamental. It helps us find out what
permission settings apply to files and directories.

##= Seeing Permissions (ls -l)

We learned earlier in this chapter that `ls` lists the contents of a
directory. When you
explored the `man` page for `ls`, perhaps you saw information about
the `-l` flag, which lists the directory contents in the "long format."
This format includes information about permissions.

Namely, if we run `ls -l` in a directory in the file system, the first
thing we see is a code with 10 permission digits, or ''bits.'' In her
_fission_ directory, Lise might see the following "long form" listing.
The first 10 bits describe the permissions for the directory contents (both files and directories):


~~~ {.bash}
~/fission $ ls -l
drwxrwxr-x  5 lisemeitner  expmt  170 May 30 15:08 applications
-rw-rw-r--  1 lisemeitner  expmt   80 May 30 15:08 heat-generation.txt
-rw-rw-r--  1 lisemeitner  expmt   80 May 30 15:08 neutron-production.txt
~~~


The first bit displays as a `d` if the target we're looking at is a
directory, an `l` if it's a link, and generally `-` otherwise. Note
that the first bit for the _applications_ directory is a `d`, for this reason.

To see the permissions on just one file, the `ls -l` command can be
followed by the filename:


~~~ {.bash}
~/fission $ ls -l heat-generation.txt
-rw-rw-r--  1 lisemeitner  expmt  80 May 30 15:08 heat-generation.txt
~~~

In this example, only the permissions of the desired file are shown.
In the output, we see one dash followed by three sets of three bits for the _heat-generation.txt_ file (`-rw-rw-r--`). Let's take a look at what this means:

- The first bit is a dash, `-`, because it is not a directory.
- The next three bits indicate that the user owner  (`lisemeitner`) can
  read (`r`) or write (`w`) this file, but not execute it (`-`).
- The following three bits indicate the same permissions (`rw-`) for the
  group owner (`expmt`).
- The final three bits (`r--`) indicate read (`r`) but not write or
  execute permissions for everyone else.

All together, then, Lise (`lisemeitner`) and her Experiment research
group (`expmt`) can read or change the file. They cannot run the file as
an executable. Finally, other users on the
network can only read it (they can never write to or run the file.)

Said another way, the three sets of three bits indicate permissions for the user
owner, group owner, and others (in that order), indicating whether they have read (`r`), write
(`w`), or execute (`x`) privileges for that file.

The `ls man` page provides additional details on the rest of the
information in this display, but for our purposes the other relevant entries here are
the two names that follow the permission bits. The first indicates that the user
`lisemeitner` is the individual owner of this file.  The second says
that the group `expmt` is the group owner of the file.


.Exercise: View the Permissions of Your Files
[TIP]
##########
. Open a terminal.

. Execute `ls -l` on the command line. What can you learn about your
files?

. Change directories to the _/_ directory (try `cd /`). What
are the permissions in this directory? What happens if you try to
create an empty file (with `touch <filename>`) in this directory?

##########

In addition to just observing permissions, making changes to
permissions on a file system is also important.

##= Setting Ownership (chown)

It is often helpful to open file permissions up to one's colleagues on
a file system. Suppose Lise, at the Kaiser Wilhelm Institute, wants to give all members of the institute permission to read and write to one of
her files, _heat-generation.txt_. If those users are all part of a group called `kwi`, then she can
give them those permissions by changing the group ownership of the file. She can handle this task with `chown`:


~~~ {.bash}
~/fission $ chown :kwi heat-generation.txt
~/fission $ ls -l heat-generation.txt
-rw-rw-r--  1 lisemeitner  kwi  80 May 30 15:08 heat-generation.txt
~~~


.Exercise: Change Ownership of a File
[TIP]
##########
. Open a terminal.

. Execute the `groups` command to determine the groups that you are a
part of.

. Use `chown` to change the ownership of a file to one of the
groups you are a part of.

. Repeat step 3, but change the group ownership back to what it was
before.

##########

However, just changing the permissions of the file is not quite
sufficient, because directories that are not executable by a given user
can't be navigated into, and directories that aren't readable by a
given user can't be printed with `ls`.  So, she must also make sure
that members of this group can navigate to the file. The next section
will show how this can be done.

##= Setting Permissions (chmod)

Lise must make sure her colleagues can visit and read the dictionary containing the file. Such permissions can be changed by using
`chmod`, which changes the file 'mode'. Since this is a directory, it
must be done in recursive mode. If she knows her home directory can be visited by members of the `kwi` group, then she can set the permissions on the entire directory tree under _~/fission_ with two commands. The
first is again, `chown`. It sets the fission directory's group
owner (recursively) to be `kwi`:


~~~ {.bash}
~ $  chown -R :kwi fission/
~~~

Next, Lise changes the file mode with `chmod`. The `chmod` syntax is
`chmod [options] <mode> <path>`. She specifies the recursive option, `-R`, then the
mode to change the **g**roup permissions, adding (`+`) reading and
e**x**ecution permissions with  `g+rx`:


~~~ {.bash}
~ $  chmod -R g+rx fission/
~~~

Many other modes are available to the `chmod` command. The mode entry
`g+rx` means we _add_ the read and execution bits to the group's
permissions for the file. Can you guess the syntax for subtracting the
group's read permissions?  The manual page for `chmod` goes into
exceptional detail about the ways to specify file permissions. Go there
for special applications.

Physicists using large scientific computing systems rely heavily on
permissions to securely and robustly share data files and programs
with multiple users. All of these permissions tools are helpful with
organizing files. Another tool available for organizing
files across file systems is the 'symbolic link.'

