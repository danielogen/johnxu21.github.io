---
layout: page
title: Dynamic Analysis- Testing
permalink: /teaching/Software-Reengineering/dynamic/
---

<form action="/teaching/Software-Reengineering/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Course Overview" />
</form>
<form action="/teaching/Software-Reengineering/metrics/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Metrics and Visualization" />
</form>
<form action="/teaching/Software-Reengineering/refactoring/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Refactoring Assistants" />
</form>
<form action="/teaching/Software-Reengineering/dynamic/">
    <input type="submit" style="background-color:firebrick;color:white;width:185px;
height:40px;" value="Dynamic Analysis: Testing" />
</form>
<form action="/teaching/Software-Reengineering/integration/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Software Integration" />
</form>
<form action="/teaching/Software-Reengineering/msr/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Mining Software Repositories" />
</form>
<form action="/teaching/Software-Reengineering/project/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Reengineering Project" />
</form>

<br/>
<br/>

Dynamic analysis is “the analysis of the properties of a running software system” 
[Ball 1999]. 
It is complementary to static analysis techniques. Some properties that cannot be 
studied through static analysis can be examined with dynamic analysis and vice versa. 
The applications of dynamic analysis techniques are very broad: program comprehension, 
system verification, resource profiling, test analysis, etc. In this session, we focus on 
one very important aspect of dynamic analysis: Testing.

As the chapter so eloquently states, "Tests: Your Life Insurance!" (OORP, p.149). 
Tests are essential for Reengineering activities. They can help you: (1) to reveal unwanted 
side effects of refactoring ("Write Tests to Enable Evolution", OORP, p.153); and (2) to 
understand the inner workings of a system ("Write tests to Understand", OORP, p.179). 

The presence of automated tests does however not offer any guarantee about its quality. 
Do the tests cover the whole system or are some parts left untested? 
Which parts are covered to which extent? Hence, measuring test coverage is a useful, even 
necessary, way to assess the quality and usefulness of a test suite in the context of reengineering.

Materials & Tools Used for this Session
===============
Slides
 - [pptx](Testing.pptx)
Projects 
- pacman-python (https://github.com/kilincceker/pacman-python-ua-sre)
- django CMS (https://github.com/django-cms/django-cms)
IDE 
- PyCharm CE (https://www.jetbrains.com/pycharm) – You can use community edition or other IDEs.
Tools
- [Coverage.py](https://coverage.readthedocs.io/en/7.10.6/) is a code coverage tool supported by PyCharm (you can use coverage tool at your discretion, but it may require some adaptations for the project we are using during the lab sessions). 
Book 
- Object-Oriented Reengineering Patterns (OORP - http://scg.unibe.ch/download/oorp)

Auxiliary Tools
==========
Auxiliary tools are not required for the lab session itself, but they may be useful to get additional information (or alternatives) on a project. Use them at your own discretion. 
- [SonarQube](https://www.sonarsource.com/products/sonarqube/) is a tool/platform that performs static analysis on source codes. It shows test coverage data from the output of dynamic analysis tools.
- [Pynguin](https://github.com/se2p/pynguin) is a test generation tool for Python. It automatically generates unit tests.

Setup / Preparation
==============
Install [Coverage.py](https://coverage.readthedocs.io/en/7.10.6/) using the code: ```pip install coverage```

Be sure to follow the setup and the tasks from the lab [Refactoring Assistants](/teaching/Software-Reengineering/refactoring/). 
Especially, the task on executing ```SonarQube```. As usual, you will also need PyCharm and projects. 
Moreover, if you have not already, download the book for this course "Object-Oriented Reengineering Patterns

Task 1 – Coverage.py Test Coverage
===========
We will begin by using the [Coverage.py](https://coverage.readthedocs.io/en/7.10.6/) test coverage tool. The testing and coverage tools should be enabled by default. Remember to install this tool using the code provided in the **setup / preparation** section above

First, make sure that you can test your Pacman-python, by using the following command line in the PyCharm terminal:

To run the tests (+ coverage) for unit test:
```yaml
python -m coverage run refactored/pacman_unittests.py
python -m coverage html
python -m coverage erase
```
And for integration test:
```yaml
python -m coverage run refactored/program_integration_tests.py
python -m coverage html
python -m coverage erase
```
This should provide you with a html coverage report with statement coverage under htmlcov directory.

Rename the generated folder for the unit and integration test you run for statement coverage. However, there are other coverage metrics available for you to analyses the adequacy of your test.

Alternatively, you can use branch coverage and include this in your html report using Coverage.py that supports statement coverage by default. To expand your report with branch coverage, check the documentation below.

To run the tests (+ coverage) for unit test: 
```yaml
python -m coverage run --branch refactored/pacman_unittests.py
python -m coverage html
python -m coverage erase
```

And for integration test:
```yaml
python -m coverage run --branch refactored/program_integration_tests.py
python -m coverage html
python -m coverage erase
```
If everything is executed without errors, you should see a new file showing the code coverage in html file under htmlcov directory. Rename the generated folder for the unit and integration test you run for branch coverage. Please try to remember this coverage (or take a screenshot to not depend on your memory).

**Related Patterns from _Object-Oriented Reengineering Patterns_ (OORP)**

- **Tests: Your Life Insurance** *(p.149)* – Tests act as a safety net during reengineering. Running coverage analysis shows where the safety net is strong or weak before making changes.
- **Grow Your Test Base Incrementally** *(p.159)* – Gradually expand your tests in small, safe steps. Here, running both statement and branch coverage provides a baseline for incremental improvement.
- **Test the Interface, Not the Implementation** *(p.171)* – Focus on verifying observable behavior rather than internal details. When analyzing coverage, look for missing behavioral scenarios, not untested private helpers.


Questions
- Is the coverage good enough?
- If you make any changes in Pacman-python sources, can you rely on the current tests to catch faults?
- Are the coverage results from statement coverage similar to the ones you got from branch coverage in the last task? Why so or why not?
- Can you apply these to django CMS project? What are your observations?

Task 2 -- Increasing Coverage on Pacman-python
===================
For the second task, we will increase the statement and branch coverage on **Pacman-python**. Doing that is very simple, we just need to write more tests. In this task, we are going to write one new test case.

Let's create a simple unit test on a method. We will test the `move_ghosts` function in `pacman.py` file in the **refactored** folder. You should look at the `pacman_unittests.py` file as a template for your test case, also in the **refactored** folder.

This activity follows the **Write Tests to Understand** pattern (*OORP*, Chapter 6, pp. 179).  
> *Intent:* When you need to understand how a part of the system works — especially if it’s not well-documented — write a small, focused test that captures your understanding. The test acts as both **documentation** and a **safety net** for future changes.  
> *In this task:* Your test for `move_ghosts` will help you learn exactly how the method behaves and record that knowledge so you can verify it in the future.


After adding the new test, run `pacman_unittests.py` again and run it with coverage. If your test has no errors, you should see the coverage report showing the code coverage. Leave this report with the coverage information on as you may need it to answer some questions (or take a screenshot of it).

Questions:
- How did the new test affect the coverage?
- Do you think 100% coverage is feasible?
- What would you propose as a good level of code coverage?

SonarQube Coverage Information on Pacman-python
===========
SonarQube is a static analysis tool. But it can show the results from test coverage in its interface. 
First, let's run SonarQube as is defined in lab TASK 3 [Refactoring Assistants](/teaching/Software-Reengineering/refactoring/). Make sure your SonarQube service is running.  Please see the documentation below for a coverage report from SonarQube.
[https://docs.sonarsource.com/sonarqube/9.8/analyzing-source-code/test-coverage/python-test-coverage/](https://docs.sonarsource.com/sonarqube/9.8/analyzing-source-code/test-coverage/python-test-coverage/)

Questions:
- What do you think of the overview coverage visualization provided by SonarQube?
- Which did you find better to visualize the source code with statement coverage, branch coverage or SonarQube?

Post-Lab Quiz: Dynamic Analysis -- Testing
==========
Posted on WebCampus
