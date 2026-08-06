# Speaker notes — How do we build competent data analysis agents?

Slides are numbered in presentation order. Untitled slides get a short description in brackets.

## Slide 1: [Title slide]

*(no speaker notes)*

## Slide 2: [Chat mockup: "Please plot hp vs mpg in mtcars"]

about a year ago, we noticed some strange behavior in a coding agent we were working on
we gave the agent a way to interpret create and interpret plots
so you could say...
make a plot of mtcars ...
interpret...
only problem is if you look closely that's not a negative relationship

## Slide 3: ❓ [The mtcars plot, full size]

so whats going on?
if you're familiar with mtcars (or cars generally) you might know that mpg should _decrease_ with horsepower
which is exactly the interpretation that the model told us
but it's not what this plot shows

## Slide 4: 🤫 [The hidden manipulation code]

behind the scenes (unbeknownst to the model)
we had manipulated mtcars to reverse the relationship between mpg and hp

## Slide 5: [Chat mockup again, no fragments]

the model appears to give us the canonical relationship
not the one that's actually in front of it

and this would happen not just with mtcars but with a variety of well known datasets
manipulate a known relationship, and the model will report what it expects to see not what's actually in the plot

there are a lot of hypotheses you might have for _why_ this is happening

one is that llms (at least at the time) just don't "see" plots very well

maybe for plots that don't contradict expectations, they are relying on other info

## Slide 6: [Random points plot]

one way to test this is to plot some basic points and ask the model to describe the number and color

## Slide 7: [Chat mockup: "3 cyan points"]

LLMs can 'see' these plots just fine

## Slide 8: posit-dev/bluffbench [blog post screenshot]

we turned this into a formal eval called bluffbench and wrote up the results

## Slide 9: posit-dev/bluffbench [original results chart]

and at the time, the leading frontier model did very poorly

We tried a bunch of stuff to drive those scores up and nothing worked.
Hilariously, we let the model "think", and in its thinking, it would say "Huh, that association doesn't look like I expect", and then turn around and say exactly what it expected to see.

## Slide 10: posit-dev/bluffbench [current results chart]

best way to drive up eval scores is just to wait 6 months
so we waited
and the scores are much better, but still less than a human would do

## Slide 11: ["Models can fail to override their own priors about data."]

*(no speaker notes)*

## Slide 12: [Blank slide]

*(no speaker notes)*

## Slide 13: [Chat mockup: "plot stress score vs sleep"]

made a second eval to test ability to spot data quality issues
suspiciously, some of the plots appear to fall directly on a straight line

## Slide 14: 🤨 [Sleep vs stress plot, full size]

stress scores against hours of sleep — overall a plausible negative trend: more sleep, less stress
but look closely: a subset of points falls _exactly_ on a straight line through the cloud
those values were imputed with a regression model
so they're not real measurements
a careful analyst would spot that suspicious artifact in the plot and investigate the data further

## Slide 15: posit-dev/bluffbench2 [results chart]

bluffbench2 is a second gen bluffbench that tests models ability to spot subtle data quality issue like the one you just saw
the frontier models do a lot worse than on bluffbench 1

## Slide 16: ["Models fail to notice subtle data quality issues that data scientists would not."]

and fail to override priors to accurately interpret plots

## Slide 17: [Dark slide: "LLMs struggle with tasks central to data analysis."]

bluffbench is evidence
llms struggle with tasks central to data analysis

both of these,

override priors
notice subtle

core skills
want any person or agent to have

## Slide 18: It's a convincing performance

but llms are great performers
can put on a convincing performance of correctness
we've seen in bluffbench that the model will interpret the plot, and will provide details and supposed evidence for what they see
they act like they are moving the analysis forward
the only problem is that, like in this case where it says this clearly negative trend is positive, is that **it's one that can fail to match reality**

## Slide 19: The answers look the same

The problem with this performance just that the model is sometimes wrong, it's that the wrong answers are confident and can be indistinguishable from the right ones

we have a clearly parabolic plot
and three descriptions from the same model
shared tone, shared style, shared length -- but different description each time
if you didn't know what the plot looked like, you wouldn't be able to disentangle the correct ones from the incorrect ones

## Slide 20: [Dark slide: "1. LLMs struggle with tasks central to data analysis."]

not only do they fail, they cloak incorrect answers in confident descriptions
makes it hard to tell what is correct or not

## Slide 21: [Dark slide: "2. LLMs are still useful for data analysis."]

don't need to throw the whole thing away

We shouldn't take evidence like bluffbench to mean that LLMs are useless _anywhere_ you value correctness.

Instead, you have to figure out where they can fit and how to design around their limitations.

## Slide 22: [Light slide: "2. LLMs are still useful" + two bullets]

prevention
mitigation

## Slide 23: We'll be talking about agents [definition bullets]

*(no speaker notes)*

## Slide 24: We'll be talking about agents [use or build one]

you might already use an agent
can also make one yourself

most relevant if you have control of the agent yourself

## Slide 25: Posit Assistant

posit agent
general purpose coding and data analysis

use as an example

## Slide 26: [TOC slide: "Make it easy for them to be right" emphasized]

need to make it easy for the models to be right

## Slide 27: Build the environment so that it's easy to be right

build an environment that makes it as likely as possible that your agent will fall into correctness

don't control the model control the harness

## Slide 28: Code as the foundation

doesn't necessarily need to write code
could imagine an agent...

llms are good at code

we've learned from years of code for analysis...

have the agent write code

## Slide 29: Design the harness with correctness in mind

Prompting: prioritize correctness over just moving the analysis forward.
Posit Assistant ... " openness to uncertainty and subtlety, and a commitment to statistical rigor ... Rather than maintaining a feeling of "moving forward," call out ambiguities and unclear results.""
Tools: the ability to run code and see your session.
Context: give it broad access to your files and environment, so it has a good chance at knowing the correct context to solve the problem.

## Slide 30: Performance improves when the environment makes it easy to be right

performance improves when ...
and it turns out all of this can make a difference even without affecting the underlying model
when we tried interventions in isolation in bluffbench, limited success
by posit assistant largely does better than minimal harness (default bluffbench case)

## Slide 31: [TOC slide: "Make it matter less when they're wrong" emphasized]

even with all that, it'll still make mistakes -- humans do, models do -- so make it less bad when it's wrong

again, code as foundation

## Slide 32: Make auditing easy

probably not enough for the model to write that code
show it to the user when it matters
make it easy to see and read
small things like syntax highlighting, code styling
you don't inspect every time, but if you suspect something went wrong you have the option

## Slide 33: Shared environment

not only is the model writing code
writing it in a shared environment
it has access to your same r/python session
its not guessing what data you have loaded -- it can see into that data in the same way you can
you can audit its code by running it yourself
it can inspect your objects and rerun code you ran

this turns out to be very useful, especially for data analysis where you are often turning the same object around and looking at it from multiple angles and transforming it

you're seeing eye to eye with the agent, instead of the two of you occupying different worlds

## Slide 34: Have the model pause often [screen recording]

so we said the first way to make it less bad if the model is wrong was having it write code
the second way is having it, in some cases, pause and involve the user

posit assistant, during open ended tasks or ones it thinks need user involvement, purposefully slows down and does shorter turns

notice in the screenshot how...

this is in contrast to how it might behave without our harness, which is to move the analysis forward with lots of code and

losing the user and obscuring its uncertainties or deciding its own answers to questions
it should have asked user

## Slide 35: Have the model pause often [what's the point of an analysis?]

another reason for this though
for verification and transparency: the human has to keep pace and see eye-to-eye with the agent (same environment).

## Slide 36: [Dark recap slide]

*(no speaker notes)*

## Slide 37: [Thank you slide]

*(no speaker notes)*
