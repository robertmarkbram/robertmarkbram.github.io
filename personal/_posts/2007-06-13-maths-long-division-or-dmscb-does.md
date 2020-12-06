---
layout: post
title: "Maths: Long Division, or DMSCB"
tags: [math]
date: 2007-06-13 01:01:01 +1000
---

Maths: Long Division, or DMSCB - Does McDonalds Sell Cheese Burgers. How long division is taught, because my daughter needed to know! [Blogger post](https://robertmarkbram.blogspot.com/2007/06/maths-long-division-or-dmscb-does.html).

I might be a <a href="http://RobertMarkBramProgrammer.blogspot.com">programmer</a>, but it was recently revealed to me that I couldn't remember how I was taught long division in school.

Finished laughing at me yet?

I searched around and found the technique, but didn't find enough examples that I understood the technique again straight away. Here is my attempt to clear that up for anyone else who forgets how to do long division (not that I am implying anything of course...).

The rule: <b>D</b>oes <b>M</b>cDonalds <b>S</b>ell <b>C</b>heese <b>B</b>urgers.

A.K.A. DMSCB
A.K.A. <b>D</b>ivide <b>M</b>ultiply <b>S</b>ubtract <b>C</b>heck <b>B</b>ring down.

Example: 956 / 18.

<b>D</b>ivide 
<pre>
   +-----
18 |  956

18 into 9? No.
18 into 95? 4 times.

       4
   +-----
18 |  956
</pre>

<b>M</b>ultiply 
<pre>
       4
   +-----
18 |  956

4 * 18 = 72

       4
   +-----
18 |  956
      72
</pre>

<b>S</b>ubtract
<pre>
       4
   +-----
18 |  956
      72

95 - 72 = 23

       4
   +-----
18 |  956
    - 72
    -----
      23
</pre>

<b>C</b>heck <i>that that the result of your subtraction is smaller than your divisor.</i>
<pre>
       4
   +-----
18 |  956
    - 72
    -----
      23

23 > 18 ... made a mistake! 
</pre>

Start over again.. 

<b>D</b>ivide 
<pre>
   +-----
18 |  956

18 into 9? No.
18 into 95? 5 times.

       5
   +-----
18 |  956
</pre>

<b>M</b>ultiply 
<pre>
       5
   +-----
18 |  956

5 * 18 = 90

       5
   +-----
18 |  956
      90
</pre>

<b>S</b>ubtract
<pre>
       5
   +-----
18 |  956
      90

95 - 90 = 5

       5
   +-----
18 |  956
     -90
    -----
       5
</pre>

<b>C</b>heck <i>that that the result of your subtraction is smaller than your divisor.</i>
<pre>
       5
   +-----
18 |  956
     -90
    -----
       5

5 < 18 ... whew! 
</pre>

<b>B</b>ring down <i>digits that haven't been divided yet</i>.
<pre>
       5
   +-----
18 |  956
     -90
    -----
       5

Bring the 6 from 956 down to the 5, to make 56.

       5
   +-----
18 |  956
     -90
    -----
       56
</pre>

And repeat.. 

<b>D</b>ivide <b>M</b>ultiply <b>S</b>ubtract <b>C</b>heck <b>B</b>ring down.
<pre>
       5
   +-----
18 |  956
     -90
    -----
       56

18 into 5? No.
18 into 56? 3 times.

       53
   +-----
18 |  956
     -90
    -----
       56
</pre>

<b>M</b>ultiply 
<pre>
       53
   +-----
18 |  956
     -90
    -----
       56

3 * 18 = 54

       53
   +-----
18 |  956
     -90
    -----
       56
       54
</pre>

<b>S</b>ubtract 
<pre>
       53
   +-----
18 |  956
     -90
    -----
       56
       54

56 = 54 = 2

       53
   +-----
18 |  956
     -90
    -----
       56
      -54
    -----
        2
</pre>

<b>C</b>heck 
<pre>
       53
   +-----
18 |  956
     -90
    -----
       56
      -54
    -----
        2

2 < 18
</pre>

<b>B</b>ring down... nothing to bring down, so we have our answer.
<pre>
       53 R 2
   +-----
18 |  956

956 / 18 = 53 R 2
</pre>


Another example with much better presentation (but without the Cheese) is given by <a href="http://www.mathsisfun.com/long_division2.html">mathsisfun.com</a>.

The first example of DMSCB that I found in my search helped a lot. It is on <a href="http://www.angelfire.com/nj4/smaclachlan0623/dmscb.html">Shannan's Math Web Site</a> on AngelFire, but I didn't find it sufficient because it didn't explain the Cheese. :)

On the same site I found an example of another technique, which I think is more useful for numbers larger than 4 digits. Check out this <a href="http://www.angelfire.com/nj4/smaclachlan0623/partquo.html">Partial-Quotients Example</a>.

True story: I learned for myself what the <b>C</b>heese was, when I made the very mistake I showed up top (18 goes into 95 only 4 times)! Yeah, you can laugh at me now. :)