One of the challenges when doing Property-based testing (PBT) is generating data...

All that is shown using Java, Kotlin and [_jqwik_](https://jqwik.net);
many ideas can be transferred to other languages and libraries, though.

<!-- use `doctoc --maxlevel 4 README.md` to recreate the TOC -->
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
## Table of Contents  

- [Introduction](#introduction)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction


### How to do PBT in Java

I've already written a
[whole blog series](https://blog.johanneslink.net/2018/03/24/property-based-testing-in-java-introduction/)
on that topic, that's why I'm not covering the basics here.
Reading [this article in Oracle's Java Magazine](https://blogs.oracle.com/javamagazine/know-for-sure-with-property-based-testing)
will also give you an overview.

#### How jqwik Works

Ok, you're too ~~lazy~~ busy to read one of the articles above.
Here's the bare minimum of [jqwik](https://jqwik.net)'s syntax that you should understand
when you want to follow along.
[Skip](#domain-data) without regret if you already know the fundamentals.

```java
class JqwikExample {
  @Property
  void stringsAndEvenInts(@ForAll String anyString, @ForAll("evenNumbers") int anEvenInt) {
    System.out.println("anyString: " + anyString);
    System.out.println("anEvenInt: " + anEvenInt);
  }

  @Provide
  Arbitrary<Integer> evenNumbers() {
    return Arbitraries.integers().filter(i -> i % 2 == 0);
  }
}
```

Above you see a working and runnable but completely meaningless property.
You can start it like any JUnit 5 platform test using Gradle, Maven or your favourite IDE.
The resulting console output will look similar to:

```
anyString:  靐乺 廛䷛ 㡡㝮屠 
anEvenInt: 176926354
anyString:  孯⇳餖᭞ၖ
anEvenInt: -568892912
anyString: 谏崢 麊⃠ 栦 ꆬ  ㄵᥞꓨṯ跚პ疽퇺꨹ 冊뀃쁾䌬ص猽 툲
anEvenInt: -2147483648
anyString:  俕 ⍍畽     䂍㷼   ⬀∄髨 ⋏ ប  Ჱ 
anEvenInt: 10
...
```

The library will generate values for all parameters annotated with `@ForAll`.
It will either use the parameter's type - `@ForAll String anyString` - to invoke one of its various default generators.
Or it will refer to a `@Provider` method specified by the annotations `value()`attribute: 
`@ForAll("evenNumbers") int anEvenInt` refers to the method named`evenNumbers`,
which is supposed to return an instance of type `Arbitrary<Integer>`.

You will learn much more about the type `Arbitrary` and the diverse ways
to implement and access arbitraries in the following sections.


## Domain Data

Default generators for JDK types.

Constraining default generators when a property or function is not total.
- With existing annotations
- With referring to methods that provide arbitraries:

    Arbitraries, which form a monadic type, can be considered to be a more abstract form of data generators.
    Arbitraries are typically created by using a DSL (domain-specific language) for specifying data generation.
    They can be invoked explicitly as above, they can be pre-registered for a given type,
    or they can be collected into _domains_ and applied whenever you formulate properties for this domain.


Example: German Zip Codes
- Generate Strings: StringArbitrary, filter, map
- Wrap in ZipCode value class: Use map and exception in constructor. Disadvantage of filtering.

Interlude about Embedded Shrinking

Combining arbitraries into domain object: Person Example?

Assumptions:
- To restrict single parameter
- To keep constraints across several parameters 

Example: Poker

Dependencies between generated data

Complex, nested domain types:
- UseType
- Builders

Example: Spotify

Recursive Data
- Non-deterministic recursion
- Deterministic recursion

Advanced Stuff:
- Data from Schema eg JSON Schema
- Decision Trees: https://github.com/regehr/uniform-tree-sampling
- Program Code: https://embed.cs.utah.edu/csmith/, https://github.com/intel/yarpgen,
  https://www.fuzzingbook.org/html/03_Syntactical_Fuzzing.html


## Summary


## Feedback

Give feedback, ask questions and tell me about your own PBT experiences
on [Twitter](https://twitter.com/johanneslink).


## Sharing, Code and License

This article is published under the following license:
[Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/)

All code is [available at Github](https://github.com/jlink/model-based-testing)
