---
layout: post
title: "Cygwin bash script: open windows explorer anywhere"
tags: [bash,cygwin,script,windows explorer]
date: 2007-06-29 01:01:01 +1000
---

> A Cygwin bash script to open Windows Explorer (or whatever tool is your Windows Explorer replacement) from any given path, or the current directory no parameter is given. [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2007/06/cygwin-bash-script-open-windows.html).
 
<P>
Here is my take on this job. It is a small script to open Windows Explorer from any path I give it, or the current directory if I don't specify a path.
</P>

<p>
<b>Updates.</b><br>
7/07/2009 1:09:35 PM. Updated handling of paths according to <a href="http://www.blogger.com/profile/10415943317265697903">Igor Mikushkin</a>'s suggestion in the comments - thanks Igor!
</p>

<P>
<b>Purpose.</b> Open Windows Explorer to any path from a Cygwin bash shell.
</P>

<P>
<b>Input.</b> Alternative path if current path is not to be used.
</P>

<P>
<b>Output.</b> Windows Explorer, opened to a given path.
</P>

<P>
<b>Code.</b>
</P>

<pre><code class="bash">#!/bin/bash
# I have called this script "windows" in my bin dir.
usage ()
{
 echo "Open Windows Explorer"
 echo "Usage: $0 [-help] [path]"
 echo "          [path]: folder at which to open Windows Explorer, will"
 echo "              default to current dir if not supplied."
 echo "          [-help] Display help (this message)."
}


location=.
case "$1" in
 ""                 ) location=.;;
 "-help"            ) usage; exit 0;;
 *                  ) location="${1}";;
esac

if [ -e "$location" ]
then
   WIN_PATH=`cygpath -w -a "${location}"`
   cmd /C start "" "$WIN_PATH"
else
 echo ${location} does not exist!
 exit 2
fi
</code></pre>


<P>
One further advantage to this script is when I use it in conjunction with my script to <a href="http://robertmarkbramprogrammer.blogspot.com/2007/06/cygwin-bash-script-listopen-arbitrary.html">search/list/open files</a>, which I have named "u" in my bin directory. Using these two, I can open a Word doc, for example, with code :
</P>

<code>
u -e windows someWordDoc.doc
</code>

<P>
It will search for the file, so I don't need its path. It will then get the Windows OS to handle opening the file.
</P>

<P>
I could also set up an alias to word directly:
</P>

<code>
alias word="/cygdrive/b/Program\ Files/Microsoft\ Office/Office/winword.exe"
</code>

<P>
In this case I could use the following to open the word doc:
</P>

<code>
word /path/to/someWordDoc.doc
</code>

<P>
The problem I found with this is that I kept getting an "install" action occurring each time I used word this way - plus, I need to know the path each time. Thus, I found it less painful to use my earlier script. :)
</P>

