# How to write tests

## The sample problem
- treat the first command line argument as a file name
- load the target of our greeting from the contents of that file
- say hello to that target
- say how long the entire process took

## The types on non-determinism
Some parts of this program will be controlled by the rules of the programming lanugage,
other parts of this program will be delegated to the operating system.
I will refer to the parts controlled by the rules of the programming language as the "deterministic" parts.
I will refer to the parts not controlled by the rules of the programming language as the "non-deterministic" parts.

So what is non-deterministic here
1. Passing the file name in as a command line argument
2. Loading the contents of a file from the file system
3. Emitting lines to the console
4. Asking what time it is

When we have to test non-determinism, the answer is always the same.
Design your application so that
- the non-deterministic part is isolated from all the parts you have control over
- the non-deterministic part can be swapped out with a deterministic version for testing.


## Tests influence the design
Start with what we want to see.
If you are are stuck, you can always start with something like

```kotlin
assertEquals(expected, actual)
``` 

This will force you to think about what you should be expecting,
as well as where the actual value is going to come from.

If you are still stuck, it is probably time to switch to prototyping.
When prototyping, don't worry about tests, just worry about how the technologies you are using work and if they are appropriate to the task at hand.
You may find you have been trying to solve a problem you didn't have.
Whatever you do, don't let that prototype code influence your design.
Prototyping is about gaining information, testing is about having a good desing.
So once you have gotten the information you need from your prototype, it is time to start the test driven design separately.

In our case, we know our expectations well enough to not get stuck.
We expect two lines, one saying hello to the target, another saying how long it took.
So lets model them like this.

```kotlin
assertEquals("Hello, world!", firstLine)
assertEquals("Took 230 milliseconds", secondLine)
```

This leads us to having to determine where the lines come from

```kotlin
val firstLine = linesEmitted[0]
val secondLine = linesEmitted[1]
```

Which in turn makes us discover that something has to be in charge of the console output,
lets call it console.

```kotlin
val linesEmitted = console.linesEmitted
```

Note that each block here gets placed ABOVE the previous block,
so what we have so far looks something like this

```kotlin
val linesEmitted = console.linesEmitted
val firstLine = linesEmitted[0]
val secondLine = linesEmitted[1]
assertEquals("Hello, world!", firstLine)
assertEquals("Took 230 milliseconds", secondLine)
```

An unintuitive side effect of doing top down design,
is that the lines tend to be written bottom up.

Now the console is not controlled by the rules of the programming language,
it is controlled by the operating system,
so this is non-determinism.
As discussed before, isolate it, and swap it out with a deterministic version for testing.

This is where things like fakes, stubs, spies, dummies, doubles, and mocks come into play.

## Fakes, stubs, and mocks
To not get bogged down in excessive detail,
I am going to present some simplified definitions of these that are good enough for now.
All of these provide an easier to test replacement for something, which I will refer to as the "subject" 
- A "Mock" allows you to specify what is expected to be received, returned, and invoked, which can then be verified by the test.
  It is kind of a record/playback library.
  You specify the responses from things the subject is expected to invoke.
  The mock records what actually happened.
  The test can query the subject regarding what was invoked, and with what parameters.  
- A "Stub" is a simplified version of the subject, that only behaves in the way the test needs it to behave. 
- A "Fake" is an implemented version of the subject, that takes a shortcut to make it deterministic.

As refactoring consolidates duplicate code from Stubs,
they tend to become more and more like Fakes.

If you start with Stubs defined for each particular test,
and refactor as you spot duplication in the stubs,
you will end up with code that is not only easy to maintain,
but also easy to add new tests for,
as you will have built up a large domain specific test library,
in the form of your well factored Stubs and Fakes.

I personally never use mocks,
as I feel they tie your tests too closely to implementation details,
and make it harder to refactor duplication in your test code.


## Our first stub, top down design

Top down design is about pretending what you need already exists,
invoking the thing you need as if it does exist,
and only creating an actual implementation when the compiler or tests force you to.

So for our stub, start with this line

```kotlin
val console = ConsoleStub()
```

The compiler leads us to this implementation

```kotlin
class ConsoleStub{
    fun linesEmitted():List<String> = TODO()
}
```

