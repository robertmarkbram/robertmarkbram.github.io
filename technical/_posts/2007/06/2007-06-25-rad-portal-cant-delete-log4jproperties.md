---
layout: post
title: "RAD - portal can't delete log4j.properties"
tags: [log4j,rad]
date: 2007-06-25 01:01:01 +1000
---

> IBM Rational Application Developer for WebSphere Software had a problem where periodically it fails to build a project, stating that it cannot delete `log4j.properties`. [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2007/06/rad-portal-cant-delete-log4jproperties.html).

<p>Sometimes when I am working in RAD (6 or 7) on a Portal (5.1) project, it fails to re-build, saying that it cannot delete <b><code>./web/WEB-INF/classes/log4j.properties</code></b>.</p>

<p>Often I can solve this with <b><code>rm -f ./web/WEB-INF/classes/log4j.properties</code></b>, but not always. Other times I need to re-build the app and delete the command quickly while that starts!</p>

<p>Worst case: I have had to close the server. Once I had to close RAD itself.</p>
