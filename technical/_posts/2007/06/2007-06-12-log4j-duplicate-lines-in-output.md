---
layout: post
title: "Log4J: duplicate Lines in output?"
tags: [content assist,content completion,eclipse]
date: 2007-06-12 01:01:01 +1000
---

> Log4J: duplicate Lines in output? [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2007/06/log4j-duplicate-lines-in-output.html).

<p>Ever see duplicate lines in your log4j output? Something like this perhaps:</p>

<pre style="color:red">2006-08-12 18:30:00,783 DEBUG  com.cml.etech.eForms.sys.AbstractQuartzJobRouter - Start of execute
2006-08-12 18:30:00,783 DEBUG  com.cml.etech.eForms.sys.AbstractQuartzJobRouter - Start of execute
2006-08-12 18:30:00,783 DEBUG  com.cml.etech.eForms.sys.QuartzJobServletRouter - About to issue getContent() request
2006-08-12 18:30:00,783 DEBUG  com.cml.etech.eForms.sys.QuartzJobServletRouter - About to issue getContent() request
</pre>

<p>The reason could be duplicate loggers specified in your log4j configuration. For example:</p>

<pre><code class="properties">log4j.rootLogger=INFO, file
log4j.logger.com.cml.etech=debug, file
</code></pre>

<p>This is telling log4j that the root logger should output to a file appender called "file" and that all classes in com.cml.etech should also log to a file appender called "file". The fix is easy: just remove "file" from the second logger definition, as per below.</p>

<pre><code class="properties">log4j.rootLogger=INFO, file
log4j.logger.com.cml.etech=debug
</code></pre>
