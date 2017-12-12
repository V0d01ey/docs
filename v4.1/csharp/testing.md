# Unit testing

The `WebSharper.Testing` library (available on [NuGet](https://www.nuget.org/packages/websharper.testing)) exposes a clean helper methods to write unit tests for libraries, services and websites.
It is a wrapper around `QUnit` for presentation which provides transparent handling of synchronous and asynchronous expressions, and generating random data for property testing.
The tests page is meant to be ran in a browser, but the server-side of a website can be tested too by making remote calls from the tests.

All functionality within are accessible with the `WebSharper.Testing` namespace.
Code samples below are also assuming that the module or assembly containing them is annotated with `[JavaScript]` except
when referring to being server-side code.

## Contents

- [Categories and tests](#categories-and-tests): organizing and running tests
    - [TestCategory](#testcategory)
    - [Test](#test)
    - [TestGenerator](#testgenerator)
- [Assertions](#assertions): simple assertions
    - [IsTrue](#istrue)
    - [IsFalse](#isfalse)
- [Equality checks](#equality-checks): equality/non-equality checkers
    - [Equal](#equal)
    - [NotEqual](#notequal)
    - [JsEqual](#jsequal)
    - [NotJsEqual](#notjsequal)
    - [StrictEqual](#strictequal)
    - [NotStrictEqual](#notstrictequal)
    - [DeepEqual](#deepequal)
    - [NotDeepEqual](#notdeepequal)
    - [PropEqual](#propequal)
    - [NotPropEqual](#notpropequal)
    - [ApproxEqual](#approxequal)
    - [NotApproxEqual](#notapproxequal)
- [Exception testing](#exception-testing): check for a raised exception
- [Sample value generators](#sample-value-generators): compose complex test values 
    - [RandomValues.Generator](#randomvaluesgenerator)
    - [RandomValues.Sample](#randomvaluessample)
    - [Random generator constructors](#random-generator-constructors)
    - [Random generator combinators](#random-generator-combinators)
    - [Use randomly generated values](#use-randomly-generated-values)

## Categories and tests

### TestCategory

`TestCategory` is a class which you can inherit from to create a class that houses a number of tests.
It has many methods that you can use in your test methods to define assertions with an optional name.
You also need to add a `Test` attribute, optionally with a string argument to name the category (default name is the class' name).

#### Example:

```csharp
[Test("My tests")]
public class MyTests : TestCategory {
    [Test("Equality")]
    public static void Equality() {
        Equal(1, 1);
    }
}
```

### Test

The `Test` attribute can be used on methods too, to mark them as a test case.
You can optionally provide a string argument to name the test (default name is the method's name).
The method must follow these rules:

* It must be returning either `void` or `Task`.
* It must be an instance method. A new instance of the containing class will be created for running each test.
* It can no or a single parameter, in the latter case, random values will be created for it based on the type of the parameter.
  Supported types are `int`, `double`, `bool`, `string`, `object` and also tuples, arrays, `IEnumerable` and `List` made from these.
  Using the `object` type results in values from mix of various types.
  When using a non-supported type, it results in a compile-time error.

#### Example:

```csharp
[Test("My tests")]
public class MyTests : TestCategory {
    [Test("One is one")]
    public static void Equality() {
        Equal(1, 1);
    }
    
    [Test("Remote.GetOne")]
    public Task TestRemote() {
        Equal(await Remote.GetOne(), 1);
    }

    [Test("Equality is reflexive")]
    public Task TestRemote(int x) {
        Equal(x, x);
    }
}
```

### TestGenerator

You must include a method like this in one of your classes:

```csharp
    [Generated(typeof(TestGenerator))]
    public static WebSharper.IControlBody RunTests() => null;
```

This runs a compile-time generator, to create a JavaScript body for this method that
runs all tests discovered in the current project.

It returns an `IControlBody`, which can be used inside the `client (() => ...)` helper to serve a page that runs the given tests,
or call its `ReplaceInDom` method directly on the client, to replace a placeholder DOM element to the content generated by `QUnit`.

#### Example:

```csharp
[JavaScript]
public class TestRunner {
    [Generated(typeof(TestGenerator))]
    public static IControlBody RunAllTests() => null;
}
```

Later, in server-side code, serve this on a [Sitelet](sitelets.md) endpoint:

```csharp
    Content.Page(
        Title = "Unit tests",
        Body = client(TestRunner.RunAllTests)
    )
```

### Expect

Tells the testing framework how many test cases are expected to be registered for this category.
If the category would register no tests, `QUnit` reports it as a failed test case, unless you add `Expect(0)`.

#### Example:

```csharp
[Test("My Tests")]
public class MyTests : TestCategory {
    [Test]
    public void Equality() {
        Expect(2);
        Equal(1, 1);
        NotEqual(1, 2);
    }    

    [Test("Not throwing")]
    public void NotThrowing() {
        Expect(0);
        // a call to some outside method:
        OtherObject.DoNotFail();
        // if this throws an error, that still fails the test case, otherwise ok,
        // even though we are not registering any assertion
    }
}
```

## Assertions

### IsTrue

Checks if a single argument expression evaluates to `true`.

#### Example:

```csharp
    [Test]
    public void Equality() {
        IsTrue(1 = 1);
        IsTrue(1 = 1, "One equals one");
    }
```

### isFalse

The negated versions of the above, the test passes if the expression evaluates to `false`.

#### Example:

```csharp
    [Test]
    public void Equality() {
        IsFalse(1 = 2);
        IsFalse(1 = 2, "One equals two is false");
    }
```

## Equality checks

### Equal

Checks if the two expressions evaluate to equal values as tested with WebSharper's equality.
This is the same as using the `==` operator from C# code, 
overrides on `Equals` method or implementing `IEquatable` is respected.

#### Example:

```csharp
    [Test]
    public void Equality() {
        var a = MyObj(1);
        var b = MyObj(1);
        Equal(a, b); // ok if MyObj overrides .Equals for structural equality
        Equal(a, b, "MyObj equality works")
    }
```

### NotEqual

The negated version of the above, fails if values are equal with WebSharper's equality.

### JsEqual

Checks if the two expressions evaluate to equal values as tested with JavaScript's `==` operator.
This is the same as using the `==` operator in C# code on `dynamic` values,
which is directly translated to `==` in JavaScript.

```csharp
    [Test]
    public void Equality() {
        var a = MyObj(1);
        var b = MyObj(1);
        JsEqual(1, 1);
        JsEqual(1, 1, "One equals one")
    }
```

### NotJsEqual

The negated version of the above, fails if values are equal with JavaScript's `==` operator.

### StrictEqual

Checks if the two expressions evaluate to equal values as tested with JavaScript's `===` operator.
This is the same as using the `object.ReferenceEquals` method in C# code,
which is directly translated to `===` in JavaScript.

### NotStrictEqual

The negated version of the above, fails if values are equal with JavaScript's `===` operator.

### DeepEqual

Checks if the two expressions evaluate to equal values as tested with `QUnit`'s `deepEqual` function
which is a deep recursive comparison, working on primitive types, arrays, objects, regular expressions, dates and functions.

### NotDeepEqual

The negated version of the above, uses `QUnit`'s `notDeepEqual` function instead.

### PropEqual

Checks if the two expressions evaluate to equal values as tested with `QUnit`'s `propEqual` function
which compares just the direct properties on an object with strict equality (`===`).

### NotPropEqual

The negated version of the above, uses `QUnit`'s `notPropEqual` function instead.

### ApproxEqual

Compares floating point numbers, where a difference of `< 0.0001` is accepted.

### notApproxEqual

The negated version of the above, fails if the difference of the two values is `< 0.0001`.

## Exception testing

### Raises

Checks that the argument function is raising any exception, passes test if it does.

#### Example:

```csharp
    [Test]
    public void Exceptions() {
        Raises(() => throw new Exception());
        Raises(() => throw new Exception(), "Exception is expected");
    }
```

## Sample value generators

### RandomValues.Generator

A generator is a value that can hold an array of static values to always test against,
and a function that can return additional values to be tested dynamically.

It is defined in F#, so directly constructing it needs some conversions,
see the static methods below to create instances of this with clean C#.

### RandomValues.Sample

The `RandomValues.Sample` type is a thin wrapper around an array of values, exposing multiple constructors
to create from a given array or explicit or inferred generators. 

#### Example:

```csharp
    var sampleFromArray = RandomValues.Saple(new [] { 1, 2, 3 });
    var sampleFromGenerator = RandomValues.Sample(RandomValues.Int); // creates 100 values
    var sampleFromGenerator10 = RandomValues.Sample(RandomValues.Int, 10); // creates given number of values
    var sampleInferred = RandomValues.Sample<int>(); // generator logic is inferred from type argument
    var sampleInferred10 = RandomValues.Sample<int>(10); // creates given number of values
```

### Random generator constructors

The following values and function produce simple `RandomValues.Generator` values:

* `RandomValues.StandardUniform`: Standard uniform distribution sampler.
* `RandomValues.Exponential`: Exponential distribution sampler.
* `RandomValues.Boolean`: Generates random booleans.
* `RandomValues.Float`: Generates random doubles. 
* `RandomValues.FloatExhaustive`: Generates random doubles, including corner cases (NaN, Infinity).
* `RandomValues.Int`: Generates random int values.
* `RandomValues.Natural`: Generates random natural numbers (0, 1, ...).
* `RandomValues.Within(low, hi)`: Generates integers within a range.
* `RandomValues.FloatWithin(low, hi)`: Generates doubles within a range.
* `RandomValues.String`: Generates random strings.
* `RandomValues.ReadableString`: Generates random readable strings.
* `RandomValues.StringExhaustive`: Generates random strings including nulls.
* `RandomValues.Auto<T>`: Auto-generate values based on type, same as `property` uses internally.
* `RandomValues.Anything`: Generates a mix of ints, floats, bools, strings and tuples, lists, arrays, options of these.

### Random generator combinators

* `RandomValues.ArrayOf(gen)`: Generates arrays (up to lengh 100), getting the items from the given generator.
* `RandomValues.GenericListOg gen`: Similar to as above, generates `List`s.
* `RandomValues.Tuple2Of (a, b)`: Generates a 2-tuple, getting the items from the given generators.
* `RandomValues.Tuple3Of (a, b, c)`: Same as above for 3-tuples.
* `RandomValues.Tuple4Of (a, b, c, d)`: Same as above for 4-tuples.
* `RandomValues.OneOf(arr)`: Generates values from a given array.
* `RandomValues.Mix(a, b)`: Mixes values coming from two generators, alternating between them.
* `RandomValues.MixMany(gens)`: Mixes values coming from an array of generators.
* `RandomValues.MixManyWithoutBases(gens)`: Same as above, but skips the constant base values.
* `RandomValues.Const(x)`: Creates a generator that always returns the same value.

### Use randomly generated values

Once you have an instance of a `RandomValues.Sample`, you can enumerate on it in a loop to run an assertion against all the values.
Also, a `RandomValues.Generator` itself can be enumeratable, it creates a default `Sample` with 100 values automatically. 

```csharp
    [Test]
    public void RandomTesting() {
        // enumerating on a custom generator
        foreach (var arr in RandomValues.ArrayOf(RandomValues.Int))
            Equal(arr, arr);
        // converting it to a Sample explicitly allows us to determine the size of the sample too
        foreach (var arr in new RandomValues.Sample<int[]>(RandomValues.ArrayOf(RandomValues.Int), 10))
            Equal(arr, arr);
        // we can also create a sample implicitly from a type
        foreach (var arr in new RandomValues.Sample<int[]>())
            Equal(arr, arr);
    }
```