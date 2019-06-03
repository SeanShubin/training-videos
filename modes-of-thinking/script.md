# Modes of thinking

## Introduction
It takes a lot of different skills to craft well designed software.
To keep mentally organized, it is useful to keep to distinct modes of thinking while programming.
Here I am focusing on modes of thinking I will name "prototyping", "red", "green", and "refactor".
Prototyping is what you do when you don't know what test to write.
Red is designing by getting a test in place that fails because the behavior under test is not implemented.
Green is implementing to get the test to pass.
Refactor is designing by recognizing patterns and introducing the appropriate design patterns.

## Prototyping
Prototyping is what you should be doing when you don't understand something well enough to write a test for it.
Many criticisms of test driven design evaporate completely when a prototype is used to get ahead of those problems.

Ideally, prototypes should be completely separate from the code base,
but even in cases where that is not practical,
it is critical to ensure the prototype does not affect the design.

Prototyping has the singular concern of figuring out how something works quickly, with no regard to quality or suitability.
Sometimes prototyping will reveal that what seemed like a good solution is not suitable after all,
such as the library not doing what you think it did,
or having dependencies that conflict with other parts of your code,
or being way more complex or exhaustive than you need.
Sometimes it is better to implement the thing yourself rather than jump through the hoops to get a third party solution integrated.

Lets say I need to access the filesystem from a java application,
but I have not coded in java for quite some time.
My first instinct is to write unit test that stubs out the filesystem,
but I soon realize my memory of how that would work is fuzzy.
Any time it is not obvious how to test something,
that is the time to switch to prototyping mode.

I remember that java.io.File had a list() method,
but some superficial investigation reveals java released new features to the file IO library.
Now I have to make a judgement call regarding whether it is worth it to learn this new API or go with what am used to.
I don't want to assume the latest is more trouble than its worth,
but I don't want to get lost in unending details either,
so I decide to time-box my research to about an hour.
I quickly discover that the java.nio.file.Files class does everything I need,
and now that I know what it looks like,
I know how to create a fake, stub, or mock for it.
Since I now know enough to write a the test, prototyping mode is over and I am ready to start the red-green-refactor loop.

I may keep the prototype in a separate project for reference,
publish it somewhere accessible to others,
or delete it now that it has served its purpose.
For the regular code base I ensure there is no connection to the prototype code.
I don't want to incur a maintenance cost for the prototype,
and I especially don't want any decisions made without quality in mind to bleed into my production code.

## Red
"Red" refers to a failing test.
This mode is where the initial design happens.
Write your test as if everything you need has already been implemented.
The test won't initially work because the things you need are not implemented,
so start putting the required structure in place.
You have reached the goal of the "red" mode once all of the structure is in place,
and now the test is failing because the final detail is not implemented.
This forces your design to be testable,
because your design was guided as a direct result of how it could be tested.  

For example, this "green" code converts 1 and 2 to the correct roman numerals.
```
    test(1, "I");
    test(2, "II");

    private String romanNumeral(int value) {
        if (value == 1) {
            return "I";
        } else {
            return "II";
        }
    }
```
To move the code forward, we add the next roman numeral to make the code "red"
```
    test(1, "I");
    test(2, "II");
    test(3, "III");

    private String romanNumeral(int value) {
        if (value == 1) {
            return "I";
        } else {
            return "II";
        }
    }
```
Now the failing test tells us what to do next.

## Green
"Green" refers to a succeeding test.
The hardest part here is to not do anything not directly related to getting the test to pass.
Green should be the simplest and easiest thing you do.
You will be distracted by features you notice you haven't implemented,
as well as by design you realize needs to be changed.
Make a note of these, 
write them down if you need to,
but keep focused on the minimal action required to get the current test to pass.
New features and design changes will be dealt with when you are in the "red" or "refactoring" modes.
Once the test is passing, it is a good idea to commit your code before moving on to refactoring.

For example, we left our code "red"
```
    test(1, "I");
    test(2, "II");
    test(3, "III");

    private String romanNumeral(int value) {
        if (value == 1) {
            return "I";
        } else {
            return "II";
        }
    }
```
We make the minimal possible change to make our code "green"
```
    test(1, "I");
    test(2, "II");
    test(3, "III");

    private String romanNumeral(int value) {
        if (value == 1) {
            return "I";
        } else if (value == 2) {
            return "II";
        } else {
            return "III";
        }
    }
```
We do this without looking for any patterns, just get it working.

## Refactor
Refactoring is where a different kind of design happens.
Here we are looking for patterns in the existing code,
making sure we understand the context of those patterns,
and deciding if the code should be restructured.
If we have to change tests, 
it is a good time to examine if we were testing what mattered in the first place.

Be careful not to refactor too early.
Duplication is easy to spot and refactor,
but it can be to tell the difference between code that is duplicate and would change for the same reason,
and code that happens to be superficially similar,
yet changes for a different reason.
If you refactor too early, you can end up re-inlining the code later when you realize that only some of it was generic enough to refactor.

Be careful not to refactor too late.
Failing to recognize design patterns causes a lot of repetition and a lot of opportunities for mistakes.
If you have to remember to make the same change in multiple places it is easy to miss one.
Design patterns are much more than literal duplication.
It can also be structural duplication,
which is well suited to polymorphism or template functions.
There are also many more advanced patterns,
such as monads for context duplication,
or the many patterns listed in the famous "Design Patterns" book.   

One common guideline is the "rule of threes",
wait until you see the same pattern three times before you consider refactoring it.
It makes sense to be a little more aggressive with your refactorings for generic libraries,
and a little less aggressive when it comes to business logic.
Let your reusable code pop out refactorings from actual usages, 
avoid trying to predict how a piece of code will be reused.  

For example, we left our code "green"
```
    test(1, "I");
    test(2, "II");
    test(3, "III");

    private String romanNumeral(int value) {
        if (value == 1) {
            return "I";
        } else if (value == 2) {
            return "II";
        } else {
            return "III";
        }
    }
```
And we see the same pattern three times, so lets refactor it
```
    test(1, "I");
    test(2, "II");
    test(3, "III");

    private String romanNumeral(int value) {
        return repeatI(value);
    }
    
    private String repeatI(int times) {
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < times; i++) {
            stringBuilder.append("I");
        }
        return stringBuilder.toString();
    }
```

## Conclusion

If you don't know how to test it, switch to "prototyping".
Making the code "red" is where you design the code so that it is easy to test.
Making the code "green" should be trivially simple because you are not thinking about design.
Refactoring is where you design the code so that it is easy to maintain.
