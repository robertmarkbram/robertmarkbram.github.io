---
layout: post
title: Data driven tests in JUnit 5 with exception handling
tags: [java, junit, data driven tests]
date: 2018-04-16 01:01:01 +1000
---

Data driven tests in JUnit 5 with exception handling. [Blogger post](https://robertmarkbramprogrammer.blogspot.com/2018/04/data-driven-tests-in-junit-5-with.html).


<style>
.example {
    font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
    border-collapse: collapse;
    width: 100%;
}

.example td, .example th {
    border: 1px solid #ddd;
    padding: 8px;
}

.example tr:nth-child(even){background-color: #f2f2f2;}

.example tr:hover {background-color: #ddd;}

.example th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
    background-color: #4CAF50;
    color: white;
}
</style>

<p>My previous post on data driven tests with TestNG (<a href="http://robertmarkbramprogrammer.blogspot.com.au/2018/04/debug-testng-data-driven-tests-when-you.html">Debug TestNG data driven tests when you only want to debug one row of data</a>) showed how handy <strong>parameterized tests</strong> are and how to get around some limitations of TestNG in Eclipse.</p>

<p>JUnit is in my opinion a better test engine, and JUnit 5 has some great new features and offers a far superior set of tooling and mechanisms for running data driven tests, some of which I shall show here.</p>

<h2>Data driven testing in JUnit</h2>

<p>In this example, I am testing the ability to use regular expressions in Java: specifically the ability to find the first <a href="https://docs.oracle.com/javase/tutorial/essential/regex/groups.html">capturing group</a>.</p>

```java
// Method under test that applies a regex to a string, returning the first group
public String searchString(final String regex, final String stringToMatchAgainst) {
   final Pattern pattern = Pattern.compile(regex);
   final Matcher matcher = pattern.matcher(stringToMatchAgainst);
   if (matcher.matches()) {
      return matcher.group(1);
   }
   return null;
}
```

<p>A bit about this method.</p>

<ol>
  <li>
    This method accepts two parameters:
    <ol>
      <li>The regular expression, named <code>regex</code>.</li>
      <li>The string we will apply the regular expression to, in oder to find a match. It is called <code>stringToMatchAgainst</code>.</li>
    </ol>
  </li>
  <li>The method returns a string, specifically the first capturing group.
    <ul>
      <li>Quick example of regex groups. Groups are bracketed portions of a regular expression that can be referred to later as <a href="https://www.regular-expressions.info/backref.html">backreferences</a>. If you were to apply the regular expression <code>"([a-z]+) ([0-9]+)"</code> against the string <code>"letters 334"</code>, the first group would be <code>"letters"</code> and the second group would be <code>"334"</code>.</li>
    </ul>
  </li>
</ol>

<p>This is the test data I am using.</p>

<table class="example">
   <tr>
      <th>Test label</th>
      <th>Regular expression</th>
      <th>String to apply regex to</th>
      <th>Expected first group</th>
   </tr>
   <tr>
      <td>Any string matches any string.</td>
      <td>(.*)</td>
      <td>xyz</td>
      <td>xyz</td>
   </tr>
   <tr>
      <td>Pick year digits out of a date.</td>
      <td>[0-9]{2}/[0-9]{2}/([0-9]{4})</td>
      <td>31/12/2032</td>
      <td>2032</td>
   </tr>
   <tr>
      <td>No digits found.</td>
      <td>[0-9]+</td>
      <td>not a digit</td>
      <td>null</td>
   </tr>
   <tr>
      <td>First two words.</td>
      <td>(?&lt;firstTwoWords&gt;\\w+ \\w+) .*</td>
      <td>abc xyz one two</td>
      <td>abc xyz</td>
   </tr>
   <tr>
      <td>Bad regex.</td>
      <td>([0-9+)</td>
      <td>123</td>
      <td>
         PatternSyntaxException: (?s)Unclosed character class.*<br /><br />
         <small><em>(This test case will throw an exception because the regular expression is invalid.)</em></small>
      </td>
   </tr>
</table>

<p>As described earlier, the regular expressions here use <a href="https://docs.oracle.com/javase/tutorial/essential/regex/groups.html">capturing groups</a>. In the fourth data set, I have also used <a href="http://farenda.com/java/java-8-regex-match-group-named-capturing-groups/">named capturing groups</a> - a cool regex feature added to Java in JDK 8.</p>

<p>Data driven testing (a.k.a. parameterized tests) is about being able to call a unit test method multiple times, giving it the actual data used to run the tests. This is as opposed to unit tests where the data is hard-coded within the test. <a href="https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests">JUnit 5's parameterized tests</a> gives you a lot of flexibility around how to generate your test data.</p>

<ul>
  <li><code>@ValueSource</code> - an array initialised within the annotation.</li>
  <li><code>@EnumSource</code> - an enum</li>
  <li><code>@MethodSource</code> - a factory method</li>
  <li><code>@CsvFileSource</code> - a CSV file</li>
</ul>

<p>The <code>@MethodSource</code> is arguably the most flexible because you can do so many things within a method to generate the returned data, including generating the data from an array, an enum, a CSV file, even doing a database lookup or calling some other service to get the data. My example will use <code>@MethodSource</code> and will generate a <a href="https://docs.oracle.com/javase/8/docs/api/index.html?java/util/stream/Stream.html">stream</a> of JUnit 5 <a href="https://junit.org/junit5/docs/5.0.3/api/index.html?org/junit/jupiter/params/provider/Arguments.html">arguments</a>.</p>

```java
// A method that returns a stream of JUnit Arguments
private static Stream<Arguments> dataForTestSearchString() {
   return Stream.of(//
         Arguments.of("Any string matches any string.", "(.*)", "xyz", "xyz") //
         , Arguments.of("Pick year digits out of a date.", "[0-9]{2}/[0-9]{2}/([0-9]{4})", "31/12/2032", "2032") //
         , Arguments.of("No digits found.", "[0-9]+", "not a digit", null) //
         , Arguments.of("First two words.", "(?<firstTwoWords>\\w+ \\w+) .*", "abc xyz one two", "abc xyz") //
         , Arguments.of("Bad regex.", "([0-9+)", "123", "PatternSyntaxException: (?s)Unclosed character class.*") //
   );
}
```

<p>Notes about this method:</p>

<ul>
  <li>It has no annotations, but it returns a stream of JUnit specific objects - <code>org.junit.jupiter.params.provider.Arguments</code></li>
  <li>Use the Java single line comment <code>//</code> by itself to ensure a line of code gets broken off when you use automatic formatting.</li>
  <li>When creating a <a href="https://docs.oracle.com/javase/8/docs/api/index.html?java/util/List.html">Java list</a> (or array, or any collection really), putting the comma at the start of each line (after the first) makes it easy to copy and paste a line to form the next list element without having to worry about <em>removing the comma from the end of the last element</em>.</li>
</ul>

<p>Here is the <code>@Test</code> method that consumes this test data.</p>

```java
// JUnit5 @Test method whose data comes from dataForTestSearchString
@ParameterizedTest(name = "#{index} - [{0}]")
@MethodSource("dataForTestSearchString")
public void testFilesFromDirectoriesAndPattern(final String label, final String regex,
      final String stringToMatchAgainst, final String expected) {
   final String actual = searchString(regex, stringToMatchAgainst);
   assertEquals(expected, actual);
}
```

<p>Notes about this method.</p>

<ul>
  <li>The <a href="https://junit.org/junit5/docs/5.0.2/api/index.html?org/junit/jupiter/params/ParameterizedTest.html">@ParameterizedTest annotation</a> lets you specify the "name" of each test data, which affects how each call to your test method is labelled in Eclipse. I have used the pattern <code>#{index} - [{0}]</code>, which means each test case will be labelled with the index of that test data and whatever the first argument to the test method is (so the first argument becomes the test case label). See the screenshot below to see how wonderful this is.</li>
    <li>
      The <a href="https://junit.org/junit5/docs/5.0.2/api/index.html?org/junit/jupiter/params/provider/MethodSource.html">@MethodSource annotation</a> allows you to name the method (or give an array naming multiple methods) that will generate data to be consumed by this test.
      <ul>
        <li>In the background, JUnit will use reflection to call the method, which has a side effect in Eclipse: if nothing else calls that method, Eclipse will give you a warning that your data generating method is unused (because it cannot work out from this annotation that you are really using it).</li>
      </ul>
  </li>
</ul>

<p>This is what you see in Eclipse when running this class as a JUnit test.</p>

![eclipse-running-junit.png](/assets/technical/images/2018/eclipse-running-junit.png)

<p>One of the most important advantages of JUnit in Eclipse over TestNG is that you can run individual test cases by right-clicking on the one you want to re-run and select "Run" or "Debug" (<a href="http://robertmarkbramprogrammer.blogspot.com.au/2018/04/debug-testng-data-driven-tests-when-you.html">TestNG would re-run the whole lot</a>).</p>

![eclipse-run-individual-test.png](/assets/technical/images/2018/eclipse-run-individual-test.png)

<p>Here is version 1 the full test case, incorporating the method being tested.</p>

```java
// First version of the data driven test, including the method being tested
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.stream.Stream;

import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.Arguments;
import org.junit.jupiter.params.provider.MethodSource;

public class JunitDataDrivenTest {

   /**
    * @return test data. Each set of arguments should consist of
    *         <ol>
    *            <li>test label</li>
    *            <li>regex</li>
    *            <li>string we apply the regex to</li>
    *            <li>first group matched by the regex</li>
    *         </ol>
    */
   @SuppressWarnings({ "unused" }) // Eclipse thinks this method is not used.
   private static Stream<Arguments> dataForTestSearchString() {
      return Stream.of(//
            Arguments.of("Any string matches any string.", "(.*)", "xyz", "xyz") //
            , Arguments.of("Pick year digits out of a date.", "[0-9]{2}/[0-9]{2}/([0-9]{4})", "31/12/2032", "2032") //
            , Arguments.of("No digits found.", "[0-9]+", "not a digit", null) //
            , Arguments.of("First two words.", "(?<firstTwoWords>\\w+ \\w+) .*", "abc xyz one two", "abc xyz") //
            , Arguments.of("Bad regex.", "([0-9+)", "123", "PatternSyntaxException: (?s)Unclosed character class.*") //
      );
   }

   /**
    * @param label
    *           description of the current test. Data is used for reporting only.
    * @param regex
    *           regular expression.
    * @param stringToMatchAgainst
    *           string that we will apply the regex to
    * @param expected
    *           first group matched by the regex. Will be null if no match is expected.
    */
   @ParameterizedTest(name = "#{index} - [{0}]")
   @MethodSource("dataForTestSearchString")
   public void testFilesFromDirectoriesAndPattern(final String label, final String regex,
         final String stringToMatchAgainst, final String expected) {
      final String actual = searchString(regex, stringToMatchAgainst);
      assertEquals(expected, actual);
   }

   /**
    * A method being tested.
    *
    * @param regex
    *           regular expression.
    * @param stringToMatchAgainst
    *           string that we will apply the regex to
    * @return first group matched by the regex. Will be null if no match is expected.
    */
   public String searchString(final String regex, final String stringToMatchAgainst) {
      final Pattern pattern = Pattern.compile(regex);
      final Matcher matcher = pattern.matcher(stringToMatchAgainst);
      if (matcher.matches()) {
         return matcher.group(1);
      }
      return null;
   }
}
```

<h2>Testing for exceptions</h2>

<p>Another new aspect of JUnit 5 is the set of <a href="https://junit.org/junit5/docs/current/user-guide/#writing-tests-assertions">assertions that use lambdas from JDK 8</a>, particularly <a href="https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/Assertions.html#assertThrows-java.lang.Class-org.junit.jupiter.api.function.Executable-">the assertThrows method</a> which I will demonstrate next.</p>

<p>As mentioned above, the last test case throws an exception because of an invalid regular expression (<code>"([0-9+)"</code> doesn't close the square brackets: it should be <code>"([0-9]+)"</code>). A normal part of unit testing should be ensuring that code throws appropriate exceptions when something goes wrong. Here is the unit test adjusted so that if our parameterized data indicates that an exception is expected, we test for it. We test for the expected exception type and have a regular expression to test the exception's message.</p>

```java
// JUnit test that checks if code throws an exception if our parameterized data indicates one is expected
@ParameterizedTest(name = "#{index} - [{0}]")
@MethodSource("dataForTestSearchString")
public void testFilesFromDirectoriesAndPattern(final String label, final String regex,
      final String stringToMatchAgainst, final String expected) {

   // Types of exceptions we test for.
   final String patternSyntaxException = "PatternSyntaxException: ";

   // PatternSyntaxException
   if (expected != null && expected.startsWith(patternSyntaxException)) {
      final PatternSyntaxException pse = assertThrows(PatternSyntaxException.class, () -> {
         searchString(regex, stringToMatchAgainst);
      });
      final Pattern exceptionPattern = Pattern.compile(expected.substring(patternSyntaxException.length()));
      final Matcher exceptionMatcher = exceptionPattern.matcher(pse.getMessage());
      assertTrue(exceptionMatcher.matches());
   } else {
      // Normal operation.
      final String actual = searchString(regex, stringToMatchAgainst);
      assertEquals(expected, actual);
   }

}
```

<p>Notes about this method.</p>

<ul>
  <li>We use one of the method parameters, a string named <code>expected</code> to tell the method what result we should expect under normal (successful) operation.</li>
  <li>
    I have extended the use of that string to also tell me what to expect in exceptional circumstances. In my code, I know what exceptions I am testing for and have decided that for test cases that should throw an exception, the <code>expected</code> string should start with the class name of the exception and the rest of the string will be a regular expression that should match the exception's message (<a href="https://docs.oracle.com/javase/8/docs/api/java/lang/Throwable.html#getMessage--">getMessage()</a>).
    <ul>
      <li>To do this I need some string processing: a test to see if the string starts with an exception class name and then a substring operation to pull out the regex.</li>
      <li>I then need code to create the <code>Pattern</code>, the <code>Matcher</code> and then test if the regex matches the exception message.</li>
    </ul>
  </li>
</ul>

<p>The unit test now passes in all cases.</p>

![eclipse-all-tests-pass.png](/assets/technical/images/2018/eclipse-all-tests-pass.png)

<p>Here is the final version of the full test case, incorporating exception testing.</p>

```java
// Final version of the data driven test including exception testing
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.regex.PatternSyntaxException;
import java.util.stream.Stream;

import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.Arguments;
import org.junit.jupiter.params.provider.MethodSource;

public class JunitDataDrivenTest {

   /**
    * @return test data. Each set of arguments should consist of
    *         <ol>
    *         <li>test label</li>
    *         <li>regex</li>
    *         <li>string we apply the regex to</li>
    *         <li>first group matched by the regex</li>
    *         </ol>
    */
   @SuppressWarnings({ "unused" }) // Eclipse thinks this method is not used.
   private static Stream<Arguments> dataForTestSearchString() {
      return Stream.of(//
            Arguments.of("Any string matches any string.", "(.*)", "xyz", "xyz") //
            , Arguments.of("Pick year digits out of a date.", "[0-9]{2}/[0-9]{2}/([0-9]{4})", "31/12/2032", "2032") //
            , Arguments.of("No digits found.", "[0-9]+", "not a digit", null) //
            , Arguments.of("First two words.", "(?<firstTwoWords>\\w+ \\w+) .*", "abc xyz one two", "abc xyz") //
            , Arguments.of("Bad regex.", "([0-9+)", "123", "PatternSyntaxException: (?s)Unclosed character class.*") //
      );
   }

   /**
    * @param label
    *           description of the current test. Data is used for reporting only.
    * @param regex
    *           regular expression.
    * @param stringToMatchAgainst
    *           string that we will apply the regex to
    * @param expected
    *           first group matched by the regex. Will be null if no match is expected.
    */
   @ParameterizedTest(name = "#{index} - [{0}]")
   @MethodSource("dataForTestSearchString")
   public void testFilesFromDirectoriesAndPattern(final String label, final String regex,
         final String stringToMatchAgainst, final String expected) {

      // Types of exceptions we test for.
      final String patternSyntaxException = "PatternSyntaxException: ";

      // PatternSyntaxException
      if (expected != null &amp;&amp; expected.startsWith(patternSyntaxException)) {
         final PatternSyntaxException pse = assertThrows(PatternSyntaxException.class, () -> {
            searchString(regex, stringToMatchAgainst);
         });
         final Pattern exceptionPattern = Pattern.compile(expected.substring(patternSyntaxException.length()));
         final Matcher exceptionMatcher = exceptionPattern.matcher(pse.getMessage());
         assertTrue(exceptionMatcher.matches());
      } else {
         // Normal operation.
         final String actual = searchString(regex, stringToMatchAgainst);
         assertEquals(expected, actual);
      }
   }

   /**
    * A method being tested.
    *
    * @param regex
    *           regular expression.
    * @param stringToMatchAgainst
    *           string that we will apply the regex to
    * @return first group matched by the regex. Will be null if no match is expected.
    */
   public String searchString(final String regex, final String stringToMatchAgainst) {
      final Pattern pattern = Pattern.compile(regex);
      final Matcher matcher = pattern.matcher(stringToMatchAgainst);
      if (matcher.matches()) {
         return matcher.group(1);
      }
      return null;
   }

}
```

<h2>Versions</h2>

<p>Software used in this post.</p>

<ul>
  <li>JDK 1.8 (1.8.0_40)</li>
  <li>Eclipse is <strong>Spring Tool Suite</strong> 3.9.2.RELEASE (build on Eclipse Oxygen.2 (4.7.2))</li>
  <li><strong>Junit</strong> plugin is built in</li>
  <li>JUnit dependencies in my <strong>pom.xml</strong>:

  ```xml
  <!-- JUnit Maven dependencies -->
  <!-- JUnit Jupiter is the API we write tests against. -->
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.1.0</version>
    <scope>test</scope>
  </dependency>
  <!-- Parameterised tests is separate download because it is currently an experimental feature. -->
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <version>5.1.0</version>
    <scope>test</scope>
  </dependency>
  <!-- The engined used by the IDE to run tests. JUnit is built in to Eclipse, but apparently my version of Eclipse can&#39;t run JUnit 5 tests. -->
  <dependency>
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-launcher</artifactId>
    <version>1.1.0</version>
    <scope>test</scope>
  </dependency>
  ```

  </li>
</ul>


