---
layout: post
title: "Cygwin bash script: open any file in directory tree"
tags: [bash,cygwin,script,search]
date: 2007-06-13 01:01:01 +1000
---

> Bash script to open a file in the current directory tree. [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2007/06/cygwin-bash-script-open-any-file-in.html).

<p>Open any file in the directory tree (from current directory) in your favourite text editor. Useful when I can't be bothered typing out the whole path to a file buried in the project structure - usually a <code>.java</code> file.</p><p><b>Note:</b> This script is superseded the <a href="http://robertmarkbramprogrammer.blogspot.com/2007/06/cygwin-bash-script-listopen-arbitrary.html">super charged version</a> I published later. This script can't even search for files!</p>

<p>The script.</p>

<pre><code class="bash">#!/bin/bash
# Open file in Ultraedit

if [ -e /cygdrive/c/Program\ Files/UltraEdit/uedit32.exe ] ; then
 editor=/cygdrive/c/Program\ Files/UltraEdit/uedit32.exe
elif [ -e /cygdrive/c/Program\ Files/IDM\ Computer\ Solutions/UltraEdit-32/uedit32.exe ] ; then
 editor=/cygdrive/c/Program\ Files/IDM\ Computer\ Solutions/UltraEdit-32/uedit32.exe
else
 editor=/cygdrive/c/WINDOWS/system32/notepad
fi

while [ $# -gt 0 ]
do
 file="$1"

 # If file cannot be found, search for it.
 if [ ! -e "$file" ] ; then
  file=`f -i "$file"`
  # Cannot find the file anyway.
  if [ -z "$file" ] ; then
   echo No file found: $1
   exit 2
  fi

  for newFile in $file ; do
   if [ -z "$newFile" ]
   then
    echo No file found: $1
    continue
   fi
   echo File: $newFile
   "$editor" `cygpath -w -a "$newFile"` &
  done
 else
  echo File: $file
  "$editor" `cygpath -w -a "$file"` &
 fi
 shift
done
</code></pre>