---
layout: reference13
title_md: Type mapping rules
tab: documentation
unique_id: docspage
author: Tom Bentley
---

# #{page.title_md}

On this page we document how Ceylon types are mapped to Java types 
on the JVM. The Ceylon 
compiler is responsible for implementing the rules but the rules are
largely constructed on the basis of 'the simplest thing that could possibly 
work'. Even so, the rules are sometimes complex.

## Reification

The Ceylon compiler *reifies* Ceylon types. This means it stores 
the full type information, including generic type parameters within the 
compiled code. It does this using Java annotations 
(with runtime retention). This is why it doesn't matter that the 
mappings in the table below are not one-to-one: The Ceylon compiler and runtime 
can always inspect the annotations to determine detailed (Ceylon) type 
information.

## Basic mapping rules

In general **the following table is a simplification** because the actual 
mapping rules depend on how the type is being used. For example a 
Ceylon type may map to one Java type when used in a method return type 
and a *different* Java type when used in an `extends` or `satisifes` clause. 

The mapping to primitive types is overriden by the following situations:

1. When a type is used as a generic type argument, because primitive types can't
   be used in this position on the JVM.
1. When a type is used in a position constrained by `actual` constraints. For example
   as a method return or parameter type on an `actual` method implementation whose
   `formal` definition is using a generic type parameter. Same for attribute types
   with a similar `actual` constraint. 
1. When a type is used in a position constrained by `actual` constraints that require
   the type to be using a different primitive. This is a variant of the previous
   type of constraint except it happens when you satisfy or extend a Java type
   which requires that your type be a different primitive. For example `int` instead
   of `long`.

Please note: **These mapping rules are subject to change as the compiler develops.**

<table>
  <tbody>
    <tr>
      <th>Ceylon type</th>
      <th>Java type</th>
    </tr>
    <tr>
      <td><code>ceylon.language.Anything</code></td>
      <td><code>java.lang.Object</code></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Object</code></td>
      <td><code>java.lang.Object</code></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Basic</code></td>
      <td><code>java.lang.Object</code></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Null</code></td>
      <td><code>java.lang.Object</code></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Boolean</code></td>
      <td><code>boolean</code><a href="#note1"><sup>1</sup></a></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Boolean?</code></td>
      <td><code>ceylon.language.Boolean</code><a href="#note6"><sup>6</sup></a></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Byte</code></td>
      <td><code>byte</code><a href="#note1"><sup>1</sup></a> <!--1.1--></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Byte?</code></td>
      <td><code>ceylon.language.Byte</code><a href="#note6"><sup>6</sup></a> <!--1.3.1--> <!--1.1--></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Integer</code></td>
      <td><code>long</code><a href="#note1"><sup>1</sup></a> (or <code>int</code> if <code>small</code>)</td>
    </tr>
    <tr>
      <td><code>ceylon.language.Integer?</code></td>
      <td><code>ceylon.language.Integer</code><a href="#note6"><sup>6</sup></a> <!--1.3.1--></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Float</code></td>
      <td><code>double</code><a href="#note1"><sup>1</sup></a> (or <code>float</code> if <code>small</code>)</td>
    </tr>
    <tr>
      <td><code>ceylon.language.Float?</code></td>
      <td><code>ceylon.language.Float</code><a href="#note6"><sup>6</sup></a> <!--1.3.1--></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Character</code></td>
      <td><code>int</code><sup><a href="#note1">1</a>, <a href="#note2">2</a></sup> (or <code>char</code> if <code>small</code>)</td>
    </tr>
    <tr>
      <td><code>ceylon.language.Character?</code></td>
      <td><code>ceylon.language.Character</code><a href="#note6"><sup>6</sup></a> <!--1.3.1--></td>
    </tr>
    <tr>
      <td><code>ceylon.language.String</code></td>
      <td><code>java.lang.String</code><a href="#note1"><sup>1</sup></a></td>
    </tr>
    <tr>
      <td><code>ceylon.language.String?</code></td>
      <td><code>ceylon.language.String</code><a href="#note6"><sup>6</sup></a> <!--1.3.1--></td>
    </tr>
    <tr>
      <td><code>ceylon.language.Exception</code></td>
      <td><code>ceylon.language.Exception</code> (in instantiations and <code>extends</code> clauses)<br/>
      <code>java.lang.Exception</code> (in <code>catch</code> clauses)<br/>
    </tr>
    <tr>
      <td><code>ceylon.language.Throwable</code></td>
      <td><code>java.lang.Throwable</code>
    </tr>
    <tr>
      <td><code>ceylon.language.Sequence</code></td>
      <td><code>ceylon.language.Iterable</code></td>
    </tr>
  </tbody>
</table>

All other types are not mapped.

## Java mapping rules

In addition, we map the following Java types to certain Ceylon types:

<table>
  <tbody>
    <tr>
      <th>Java type</th>
      <th>Ceylon type</th>
    </tr>
    <tr>
      <td><code>byte</code></td>
      <td><code>ceylon.language.Byte</code> <!--1.1--></td>
    </tr>
    <tr>
      <td><code>short</code></td>
      <td><code>ceylon.language.Integer</code></td>
    </tr>
    <tr>
      <td><code>int</code></td>
      <td><code>small ceylon.language.Integer</code></td>
    </tr>
    <tr>
      <td><code>float</code></td>
      <td><code>small ceylon.language.Float</code></td>
    </tr>
    <tr>
      <td><code>char</code></td>
      <td><code>small ceylon.language.Character</code><a href="#note4"><sup>4</sup></a></td>
    </tr>
    <tr>
      <td><code>boolean[], byte[], short[], int[], long[], float[], double[], char[]</code></td>
      <td><code>java.lang.BooleanArray, java.lang.ByteArray, java.lang.ShortArray, java.lang.IntArray, java.lang.LongArray, java.lang.FloatArray, java.lang.DoubleArray, java.lang.CharArray</code><a href="#note5"><sup>5</sup></a></td>
    </tr>
    <tr>
      <td><code>T[]</code></td>
      <td><code>java.lang.ObjectArray&lt;T&gt;</code><a href="#note5"><sup>5</sup></a></td>
    </tr>
    <tr>
      <td><code>java.lang.Object</code></td>
      <td><code>ceylon.language.Basic</code></td>
    </tr>
  </tbody>
</table>

Notes:
<table>
  <tbody>
    <tr>
      <td><a name="note1"><sup>1</sup></a></td>
      <td>Unless used as a type parameter, or in a position constrained by `actual` constraints.</td>
    </tr>
    <tr>
      <td><a name="note2"><sup>2</sup></a></td>
      <td>In Ceylon, characters are stored as Unicode code-points, thus requiring 32 bits.</td>
    </tr>
    <tr>
      <td><a name="note4"><sup>4</sup></a></td>
      <td>Java characters can only represent the first half of a Unicode code-point (16 bits).</td>
    </tr>
    <tr>
      <td><a name="note5"><sup>5</sup></a></td>
      <td>See more info on <a href="../java-from-ceylon#java_array_types">Java arrays</a>.</td>
    </tr>
    <tr>
      <td><a name="note6"><sup>6</sup></a></td>
      <td>Except, since Ceylon 1.3.1, for field types when in <a href="../ee-mode/">EE mode</a>.</td>
    </tr>
  </tbody>
</table>

## See also

* The [annotations](../annotations) used for reification.

