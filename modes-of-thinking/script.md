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
Ideally, prototypes should be completely separate from the code base,
but even in cases where that is not practical,
it is critical to ensure the prototype does not affect the design.

Prototyping mode is a singular concern of figuring out how something works quickly, with no regard to quality or suitability.
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
Since I now know enough to write a the test, prototyping modes is over and I am ready to start the red-green-refactor loop.

I may keep the prototype in a separate project for reference,
publish it somewhere accessible to others,
or delete it now that it has served its purpose.
For the regular code base I ensure there is no connection to the prototype code.
I don't want to incur a maintenance cost for the prototype,
and I especially don't want any decisions made without quality in mind to bleed into my production code.

## Red

## Green

## Refactor

## Conclusion

