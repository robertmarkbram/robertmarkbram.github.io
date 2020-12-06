---
layout: post
title: "Cygwin bash script: fully qualified java class name into clipboard"
tags: [bash,cygwin,script]
date: 2005-12-12 01:01:01 +1000
---

Take a Java class name and output the fully qualified Java name (package + class name). [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2005/12/fully-qualified-java-class-name.html).

<p><b>Purpose.</b> Take a Java class name and output the fully qualified Java name (package + class name). </P><p><b>Input.</b> A valid Java class name. You should be able to do the following search with it: "find . -name ClassName.java". </P><p><b>Output.</b> Fully qualified Java class name, output to the console and to the system clipboard. </P><p>Code: </P>

<pre><code class='bash'>#!/bin/bash

# Get a file name and convert '/' to '.' and place in system clipboard.
if [ $# -ne 1 ]
then
 echo Get a file name and convert '/' to '.' and place in system clipboard.
 echo Usage: $0 fileNameToConvert
 exit 1
fi

extension=
echo "$1" | grep ".java$"

if [ $? -ne 0 ]
then
 extension=".java"
fi

file=`find . -iname "${1}${extension}"`

if [ $? -ne 0 ]
then
 echo Failed to find file: $1
 exit 2
fi

# Replace path separators with package separators.
result=`echo $file | sed 's/\//./g' | sed 's/[.]java//g'`
# Remove leading source directory.
result=`echo $result | sed 's/\.\.src\.//g'`
# Remove leading dots.
result=`echo $result | sed 's/^\.\.//g'`

echo -n $result | putclip
echo $result
</code></pre>