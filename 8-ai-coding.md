# Copilot to Cover: AI-augmented coding isn't your robot overlord - It's here to help

## Where is GitHub Co-Pilot good?

1. Boiler Plate Code
2. Calling "Foreign Lands" without Googling - For example calling the twitter API.

## Diffblue Cover

Particularly about writing tests. Test writer is harder than completion.
- It requires far more context (types, methods, factories, data)
- Copilot is right about ~43% of the time, it needs to be checked by a human
- Complex interedependiices between program and data
- Practical difficulties: IO, frameworks, inversion of control, mocking, idioms

## Coding as Search of Program Space

```txt
Set of all possible test programs {
    Programs that are valid and run {
        High coverage tests {
            Tests that developers find easier to read
        }
    }
}
```

## Diffblue Reinforcement Learning

Run test -> Results -> Evaluate Effectiveness --(coverage, other metrics, existing java code)--> Predict A Better Test -> Write Test -> Run Test

## Regression Unit Tests

1. Write tests for entire app (baseline)
2. Run tests on proposed change
3. Fix bugs found by tests
4. Update tests

In a typical change the engineer writes code, makes a PR, the unit tests run if it works the PR is approved, otherwise the engineer writes more code and updates the PR.

With AI, the Diffblue regression runs the relevant tests on the PR. After merging, the Diffblue baseline is updated.

## Cover Example

IntelliJ lets you run Cover against a particular class, at which point it will use AI to write your test cases.

Cover works off the byte-code whereas Copilot works on the source code.

The engine will try different inputs against the methods in the class and see which determine useful outputs on the other side.

The generated methods will have example data that looks loosely correct into what is expected.

## DeepMind AlphaCode

Coding competitions have very precisely defined outcomes. For real development, clients will need to accurately describe what they want in order for AI to be able to complete it.

AlphaCode is a GPT, Transformed-Based AI. It's trained on coding competitions and validates on the unit tests for those coding competitions.

Like cover, it does a search through the space of solutions. It generates hundreds of solutions, then narrows down those that are semantically identical and then runs the unit tests against those solutions.

It beats programmers in this narrow space ~50% of the time.

It's not currently available outside of DeepMind.

## Summary

AI Augmented tools are real today and help eliminate tedious, error-prone coding tasks.

All of the leading tools have free editions that can be tried today.

Really things are just getting started. So let's see what happens next.

## Questions

Does Cover assume that the code that you have written is correct? What does this mean for TDD?

- Cover is complimentary to TDD. It does assume that your code is correct but it's designed to fill in the gaps that haven't been covered with your TDD tests.

With regards to the reinforcement learning, how does it get rewarded?

- You can't configure it. It's built in to cover. Diffblue are constantly refreshing to make it what developers want.

Does cover refactor, and write with best practices?

- Test names will reflect method names. We want the name of the test to reflect what it's doing, which is hard, but we can make it do that.
- Some initial support for refactoring is coming in.

How does cover stay up to date with libraries? Eg. Mockito.

- We make efforts to support the various types of frameworks that you might be using. Sometimes there are breaking changes that need to be supported but Diffblue will try and support the latest versions of libraries in a compatible way. 
