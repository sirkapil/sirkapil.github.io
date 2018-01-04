---
layout: post
title: Model based test automation - principles
tags: [testAutomation, pageObject]
excerpt_separator: <!--more-->
---

The topic of test automation frameworks is huge. All of its aspects are worth many pages of conversation. Below I tried to collect the general principles I find useful and desired to almost any (model based) test automation framework regardless of the specific details of them.

One source of the historical overview of test automation frameworks is [this paper](http://www.simplytesting.com/Downloads/Kent%20-%20From%20Rec-Playback%20To%20FrameworksV1.0.pdf) from John Kent. It does worth reading to see how we've got to the model-based automation.

<!--more-->

###General Principles

#### Test code is product code.
If it does not get treated like it, eventually the maintenance cost becomes much much greater than the benefit of having the tests.

#### The same basic programming/architecturing principles have to be applied to the test codebase as to any product codebases.
* The code should be DRY (Don't Repeat Yourself): code duplications should be punished by fire and death
* Promote the code reusability
* The code base has to have a structure, layers to it
* Refactor, Refactor, Refactor: it has its cost, but it is inevitable on the long run.

#### The code implementing the test scenario should be entirely separated from the code which defines the interaction with the target application
* Following this principle there will be at least two separate layer defined in the architecture (tests, interaction code)

#### The tests should only use a well defined API of the interaction code
* Combined with the separation principle above, this enables the test and interaction code development the be separate processes.
* This will create an N:1 relation between the tests and the interaction code. The happy consequence is that if the target application changes without the requirements change, there is only the interaction code needed to be changed

#### Version control the tests, model code and the target application together
* If the tests and the application are not versioned together it gives an accumulative maintenance cost to be able to identify that which version of the tests are compatible with which version of the application under test.

#### The test case should be as readable as possible
* See the [next blog entry]({% post_url 2016-01-10-model-based-test-automation-test-cases %}).

#### The name of the test case should describe entirely the scenario the case is implemented for
* If the case does too much to be able to capture it in the name of the function it's a good sign that the case should be divided into multiples.
* Having a structure like targetName_scenarioAndInput_expectedResult in the test case naming helps a lot. For example ElementDeletion_NotEmptyElement_AsksForConfirmation

It is intentional that I did not write "UI testing" anywhere. These principles can be used for any testing level like unit, integration, system or UI.