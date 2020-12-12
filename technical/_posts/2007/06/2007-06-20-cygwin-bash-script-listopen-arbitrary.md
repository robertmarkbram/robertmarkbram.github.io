---
layout: post
title: "Cygwin bash script: search/ list/ open arbitrary files in directory tree"
tags: [bash,cygwin,script,search]
date: 2007-06-20 01:01:01 +1000
---

> Cygwin bash script to search for files in a selected directory tree, list the results and open those that are chosen. [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2007/06/cygwin-bash-script-listopen-arbitrary.html).


<p><b>Update.</b> 13/12/2008 13:40:44 PM. Updated formatting of this article and updated the script. </p>

<p><b>Update.</b> 19/11/2007 12:31:44 PM. Added ability to follow (or not) symbolic links. </p>

<p><b>Update.</b> 9/02/2009 9:50:16 PM. Fixed (v 1.8.2 according to Anon's suggestion (time stamp Monday, February 09, 2009 9:23:00 PM). </p>

<p>This script is actually version 2 of the <a href="http://robertmarkbramprogrammer.blogspot.com/2007/06/cygwin-bash-script-open-any-file-in.html">Cygwin Bash script: open any file in directory tree</a>. It solves a wider variety of problems for me now. </p>

<p>I work with Java projects where I frequently want to list or open files without having to bother remembering or typing their complete paths - or names! Eclipse (and most other IDEs I am sure) offer the very cool "Open Type" and "Open Resource" features that allow you to type in file names using * wild cards and see a dynamic list of matching results. This doesn't provide a total solution however. Two key cases in point: </p>

<ul>
  <li>I have one project where there are 10's of classes with the same name (and thus same file name) which are discriminated by their package name i.e. path. So while I can type in "CommandBean" I still have to navigate through a large list of results to get to the one I want. </li>
  <li>Other times I don't want to open the file. I just want to list them with their paths so I can copy and paste their names/paths into something else, like a code review form or change log. </li>
</ul>

<p><a href="http://www.vim.org/">Vim</a> with William Lee's <a href="http://vim.sourceforge.net/scripts/script.php?script_id=1899">FindFile</a> plugin script almost satisfied me, but there were a number of usage points that put me off. </p>

<ul>
  <li>One more app to keep open. I would open Vim/Gvim separately to do a task I otherwise want to do within Cygwin - which I use daily for so many tasks. I already have an editor of choice - <a href="http://www.ultraedit.com/">UltraEdit</a> - so I don't really like having another editor open when I won't be using it for what it really wants to be used for.. <i>I have a Porsche and a Mercedes, but I never the drive the Mercedes, I just sit in it when I want to listen to the radio!</i> </li>
  <li>The FindFile interface is nice in that you can type and it dynamically adjusts the results, but you can only open one file per usage so you have to keep typing until the list is down to one result. This didn't suit all the scenarios I wanted it for. </li>
  <li>I like Vim/Gvim, I really do, but I like UltraEdit better, so I don't want to switch. :) </li>
</ul>

<p>So, I re-wrote my old script and came up with own solution. </p>

<p><b>Purpose.</b> List or open arbitrary files from a directory tree. </p>

<p><b>Input.</b> Various options, path names and regular expressions. </p>

<p><b>Output.</b> List of file paths/names. Opens files in your editor of choice. </p>

<p><b>Example 1:</b> </p><a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://4.bp.blogspot.com/_jwApeiH4frk/RoEKMimGQTI/AAAAAAAAADw/kbUGzNUnSuc/s1600-h/uExample1.PNG"><img style="cursor:pointer; cursor:hand;" src="http://4.bp.blogspot.com/_jwApeiH4frk/RoEKMimGQTI/AAAAAAAAADw/kbUGzNUnSuc/s400/uExample1.PNG" border="0" alt=""id="BLOGGER_PHOTO_ID_5080353065211937074" /></a>

<ol>
  <li>List all files in the directory tree with 'department' in their name/path. </li>
  <li>List only .java files whose class name ends with 'department'. </li>
  <li>List only .java files in a package name that includes 'load' and whose class name ends with 'department'. </li>
  <li>List the same files as above but offer to open them. Open all of them. </li>
</ol>

<p><b>Example 2</b> </p><a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://3.bp.blogspot.com/_jwApeiH4frk/RoELcSmGQUI/AAAAAAAAAD4/yS0P0hS39rY/s1600-h/uExample2.PNG"><img style="cursor:pointer; cursor:hand;" src="http://3.bp.blogspot.com/_jwApeiH4frk/RoELcSmGQUI/AAAAAAAAAD4/yS0P0hS39rY/s400/uExample2.PNG" border="0" alt=""id="BLOGGER_PHOTO_ID_5080354435306504514" /></a>

<ol>
  <li>Find all .java files whose class name ends with 'department' or 'location'. Open two specific files in that list. </li>
  <li>Open a specific file using its relative path from the current directory. </li>
  <li>Find all .java files whose package name include the 'base' package. Open one specific file in the result set. </li>
</ol>

<p><b>Code:</b></p>

<pre><code class="bash">#!/bin/bash

#-------------------------------------------------------------------------------
#  Editor's Little Helper. :)
#-------------------------------------------------------------------------------
# Open and/or search for a set of files and open them in your favourite text
# editor.
#
# Author: Robert Mark Bram
# v 1.2 - 21/06/2007 1:03PM. Made the script interactive
# v 1.3 - 26/06/2007 7:46AM. Modified usage comments. Fixed handling of spaces
#  in files names.
# v 1.4 - 6/07/2007 3:16:51 PM. Modified to protect against invalid indexes
#  being entered by the user.
# v 1.5 - 8/07/2007 4:31:23 PM. Split openFile function into open file by index
#  or path where each method should be called explicitly. Fixed error where
#  script was opening all by index, assuming paths were indexes!
# v 1.6 - 8/07/2007 4:37:47 PM. Split printListOfFiles function into two
#  functions, one that would print the index of each file along with the file
#  path, and one that would only print the path. Useful if this command is to
#  be part of a pipe.
# v 1.7 - 19/11/2007 12:31:44 PM. Included the ability to follow (or not) symbolic
#   links by hooking into the find command's capability for this. By default, do
#   not follow symbolic links.
# v 1.8 - 29/02/2008 3:23:20 PM. Exclude files matching:
#  grep -v '[.]svn-base$\|zzbuild\|[.]class$\|[.]bak$'
# v 1.8.1 - 12/05/2008 5:16:51 PM. Added another path for ultra edit.
# v 1.8.2 - 9/02/2009 9:50:16 PM. Fixed IFS=.. it still broke on spaces.

#-------------------------------------------------------------------------------
#  Dependencies
#-------------------------------------------------------------------------------

#-------------------------------------------------------------------------------
#  Variables for this script.
#-------------------------------------------------------------------------------
# Directory from which we search for files.
baseDir=.
# Case insensitive by default
caseSensitive=N
# Absolute path (or command string) to the editor to use when opening up files.
editor=
# Use this with the find command to search for files by name within baseDir.
fileNamePattern=*
# Interactive mode: [F]ull, [L]ist, [N]one or [P]artial.
interactivityMode=P
# If in partial interactivity, how many files do we have to find in order to
# trigger interactivity?
minFilesInteractive=2
# Did we actually have to search for files? If no, then all trailing args to
# this script were valid relative paths from base dir or valid absolute paths.
# Needed to work out what to do in partial interactivity mode.
searchPerformed=N
# Follow symbolic links? -P (no) or -L (yes) - as per the find command.
symbolic=-P


#-------------------------------------------------------------------------------
#  Common functions for this script.
#-------------------------------------------------------------------------------

# Help!
function usage () {
less << STOP_HELP
usage: $0 [-d DIRECTORY] [-e EDITOR] [-f FILE_NAME_PATTERN] [-h]
        [-i MODE] [-L] [-P] [-s] [regular expression or file path] ...

Search for a set of files and open them in your
favourite text editor

==========================
-d DIRECTORY
    Use directory other than default - which is the current dir.

-e EDITOR
    Specify an editor path or command to use, other than UltraEdit
    or notepad.

-f FILE_NAME_PATTERN
    Search pattern for file names. Argument is as per
    "find . -name FILE_NAME_PATTERN".

-h, --help
    Displays this message and exits.

-i  Define mode of interactivity. Defaults to Partial.
    - [F]ull: display lists of files and ask user what ones to open.
    - [L]ist: lists files only. No other form of interaction.
    - [N]one: do not display list of files or ask the user anything:
         automatically open all files found.
    - [P]artial: can act as Full or None as per below.
      - act as None if number of files found <= MIN_FILES_INTERACTIVE
    - act as None if all trailing args were valid relative
      paths to files from DIRECTORY or valid absolute paths.

-L  Follow symbolic links (as per find command).

-m MIN_FILES_INTERACTIVE
    Set the minimum number of files that should be found in order
    to trigger interaction when in Partial interactivity mode.
    Defaults to 2.

-P  Never follow  symbolic  links (as per find command). DEFAULT is this.


-s  Case sensitive.

==========================
General process for searching for files.

1) Find superset of files from target directory with the find command.
    - Can apply arguments to modify find command file name pattern.
    - Can apply arguments to modify target directory.
2) Apply regular expressions to superset of files to get selection set.
    - Can use one or more regular expressions via trailing arguments
      to this command.
3) Ask what files you wish to open in the editor.
    - Can apply argument to turn off interactive mode - all files get
      opened.
    - Can apply argument to set your own editor.

==========================
Examples.

$0 -i l "commandbean.java$"
  List all files from the current directory down whose relative path
  (including file name) ends with "commandbean.java" - case
  insensitive. Here it is effectievly doing:
  find . -name "*" | grep -i "commandbean.java$"

$0 -i l -f "*.java" commandbean
  List all files from the current directory down whose file name
  ends with ".java" and whose relative path - including file name -
  contains "commandbean" - case insensitive. Here it is effectievly
  doing:
  find . -name "*.java" | grep -i "commandbean"

$0 "status.*/.*java$"
$0  -f "*.java" "status.*/"
  Ask me to open files from list of Java source files in a package
  that includes the token "status" somewhere in it -- assuming there
  are more than 1.

$0 ./some/path/to/MyClass1.java \\
./some/path/to/MyClass2.java \\
./some/path/to/MyClass3.java \\
  Open up the files specified in UltraEdit.

$0 ./some/path/to/MyClass1.java ".*.properties$"
  Ask me to open files from list of a specific Java source file and
  all properties file that are found in /base/dir -- assuming the
  list has more than 1 result.

STOP_HELP

}

# Process command line arg governing interactivity.
function setUpInteractivity() {
  case "$1" in
    "F" | "f" ) interactivityMode="F";;
    "L" | "l" ) interactivityMode="L";;
    "N" | "n" ) interactivityMode="N";;
    "P" | "p" ) interactivityMode="P";;
    *         ) echo "Invalid option for -i. [F]ull, [L]imited or [N]one." ;
          usage; exit 4;;
  esac
}

# Process command line arg governing minFilesInteractive.
function setUpMinFilesInteractive () {
  if [ -n "`echo "$1" | grep -E -e '^[0-9]+$'`" ]
  then
    minFilesInteractive="$1"
  else
    echo "Invalid option for -m; not a number."
    usage
    exit 5
  fi
}

# Work out what editor to use if the user didn't specify one.
# Set the path to that editor in a script variable, "editor".
function findEditor() {
  if [ -z "$editor" ]
  then
    # Work out what editor to use.
    # Set the path to that editor in a script variable, "editor".
    if [ -e /cygdrive/c/Program\ Files/IDM\ Computer\ Solutions/UltraEdit-32/uedit32.exe ]
    then
      editor=/cygdrive/c/Program\ Files/IDM\ Computer\ Solutions/UltraEdit-32/uedit32.exe
    elif [ -e /cygdrive/c/Program\ Files/IDM\ Computer\ Solutions/UltraEdit/Uedit32.exe ]
    then
      editor=/cygdrive/c/Program\ Files/IDM\ Computer\ Solutions/UltraEdit/Uedit32.exe
    elif [ -e /cygdrive/c/Program\ Files/UltraEdit/uedit32.exe ]
    then
      editor=/cygdrive/c/Program\ Files/UltraEdit/uedit32.exe
    else
      editor=/cygdrive/c/WINDOWS/system32/notepad
    fi
  fi
}

# Open requested file in chosen editor according to an index within fileSet.
function openFileByIndex () {
  if [ -z "${fileSet[$1]}" ]
  then
    echo "No file found with index $nextIndex"
    return
  fi
  echo File: "${fileSet[$1]}"
  "$editor" `cygpath -w -a "${fileSet[$1]}"` &
}

# Open requested file in chosen editor using its path.
function openFileByPath () {
  if [ -f "$1" ]
  then
    echo File: "$1"
    "$editor" `cygpath -w -a "$1"` &
  else
    echo "No file found $1"
  fi
}

# Open files that have been selected - filesToOpen must contain a list of space
# separated indexes to the associative array of files, fileSet.
function openSelectedFiles () {
  for nextIndex in $filesToOpen
  do
    case "$nextIndex" in
      *[!0-9]*|""   ) echo "Not a valid index: $nextIndex";;
      *             ) openFileByIndex "$nextIndex";;
    esac      #  Allows ranges of characters in [square brackets],
  done
}

# Open all files in the array of files, fileSet.
function openAllFiles () {
  index=0
  while [ "${index}" -lt "${#fileSet[@]}" ]
  do
    # echo File [${index}]: ${fileSet[$index]}
    openFileByPath "${fileSet[$index]}"
    let "index++"
  done
}

# Print list of files in the set - printing index for each entry.
function printListOfFilesWithIndex() {
  index=0
  while [ "${index}" -lt "${#fileSet[@]}" ]
  do
    printf "File %*d: ${fileSet[$index]}\n" 2 ${index}
    let "index++"
  done
}

# Print list of files in the set - without an index for each entry.
function printListOfFilesWithoutIndex() {
  index=0
  while [ "${index}" -lt "${#fileSet[@]}" ]
  do
    printf "${fileSet[$index]}\n"
    let "index++"
  done
}

# Find files from the trailing arguments to this script.
# Each argument could be a
# - valid relative path to a file from base dir.
# - valid absolute path to a file.
# - regular expression to be used to grep over results of a find.
# Fill up a variable called fileSet with the results.
function getFilesFromTrailingArgs() {
  # Make delimiter equal to whatever newline char we are using now
  oldIFS=$IFS
  IFS=$'\n'

  # Go through remaining arguments.
  fileIndex=0
  while [ $# -gt 0 ]
  do
    # If the argument is a file path that exists, put it in the file set
    if [ -f "$1" ]
    then

      # echo File path: "$1"
      fileSet[$fileIndex]="$1"
      let "fileIndex++"
    else
    # Otherwise, treat argument as a regular expression to modify superset of
    # files - which is itself the result of a find command.

      # We are searching..
      searchPerformed=Y

      if [ "$caseSensitive" = "Y" ]
      then
        files=`find ${symbolic} . -type f -name "$fileNamePattern" | grep "$1" | grep -v '[.]svn-base$\|zzbuild\|[.]class$\|[.]bak$'`
      else
        files=`find ${symbolic} . -type f -iname "$fileNamePattern" | grep -i "$1" | grep -v '[.]svn-base$\|zzbuild\|[.]class$\|[.]bak$'`
      fi

      # Put each file we found into file set.
      for file in $files ; do
         # echo File from expressio: "$file"
        fileSet[$fileIndex]="$file"
        let "fileIndex++"
      done
    fi
    shift
  done

  # Reset separator to what it was.
  IFS="$oldIFS"
}

# Find files using only a find - no grep, no file paths.
# Fill up a variable called fileSet with the results.
function getFilesWithoutTrailingArgs() {
  # No trailing args - we are searching..
  searchPerformed=Y

  # Make delimiter equal to whatever newline char we are using now
  oldIFS=$IFS
  IFS="
  "

  if [ "$caseSensitive" = "Y" ]
  then
    files=`find ${symbolic} . -type f -name "$fileNamePattern"`
  else
    files=`find ${symbolic} . -type f -iname "$fileNamePattern"`
  fi

  # Put each file we found into file set.
  for file in $files ; do
    # echo File from expressio: "$file"
    fileSet[$fileIndex]="$file"
    let "fileIndex++"
  done

  # Reset separator to what it was.
  IFS="$oldIFS"
}

# Display list of files and ask user which ones to open. Puts their answer into
# variable called fileToOpen.
function askUserWhatFilesToOpen() {
  printListOfFilesWithIndex
  Echo Specify files to open. [A]ll, [N]one or [x y z] space separated indexes.
  read filesToOpen
  # echo We will open these: ${filesToOpen}

  case "${filesToOpen}" in
    "n" | "N") ;; # echo Open no files;;
    "a" | "A") openAllFiles;;
      *        ) openSelectedFiles;;
  esac
}



#-------------------------------------------------------------------------------
#  Script Logic
#-------------------------------------------------------------------------------

# Must have *some* arguments.

if [ "$#" -eq 0 ]
then
  echo Must specify command line arguments.
  usage; exit 0;
fi

# Process all the opening arguments.
exitFlag=N
while [ "$exitFlag" = "N" ]
do    # Until exit flag is set to Y.
  case "${1}" in
    "-d"    )  baseDir="$2"; shift;shift;;
    "-e"    )  editor="$2"; shift;shift;;
    "-f"    )  fileNamePattern="$2"; shift;shift;;
    "-h"    )  usage; exit 0;;
    "-i"    )  setUpInteractivity "$2"; shift;shift;;
    "-L"    )  symbolic="-L"; shift;;
    "--help")  usage; exit 0;;
    "-m"    )  minFilesInteractive="$2"; shift;shift;;
    "-P"    )  symbolic="-P"; shift;;
    "-s"    )  caseSensitive=Y; shift;;
    *       ) exitFlag=Y;;
  esac
done

cd "$baseDir"

findEditor

if [ $# -gt 0 ]
then
  getFilesFromTrailingArgs "$@"
else
  getFilesWithoutTrailingArgs
fi

# echo Found ${#fileSet[@]} files.

if [ ${#fileSet[@]} -eq 0 ]
then
  echo No files found.
  exit 0
fi

# Just list the files?
if [ "$interactivityMode" = "L" ]
then
  printListOfFilesWithoutIndex


# Open all of the files without asking? Interactive mode is [N]one.
elif  [ "$interactivityMode" = "N" ]
then
  openAllFiles

# Interactive mode is [P]Artial.
# Ask the user what files to open if no search was performed or if number of
# files found was less than min required to trigger interactivity.
elif  [ "$interactivityMode" = "P" ]
then
  if [ "${#fileSet[@]}" -lt "$minFilesInteractive" -o "$searchPerformed" = "N" ]
  then
    openAllFiles
  else
    askUserWhatFilesToOpen
  fi

# Interactive mode is [F]ull.
# Ask the user what files to open if we are in interactive mode.
else
  askUserWhatFilesToOpen
fi


# echo Done.
</code></pre>

