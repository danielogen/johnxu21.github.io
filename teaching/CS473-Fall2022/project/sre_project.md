---
layout: page
title: Reengineering Project - linkedin/kafka
permalink: /teaching/CS473-Fall2022/project/
---

<form action="/teaching/CS473-Fall2022/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Course Overview" />
</form>
<form action="/teaching/CS473-Fall2022/metrics/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Metrics and Visualization" />
</form>
<form action="/teaching/CS473-Fall2022/refactoring/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Refactoring Assistants" />
</form>
<form action="/teaching/CS473-Fall2022/integration/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Software Integration" />
</form>
<form action="/teaching/CS473-Fall2022/dynamic/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Dynamic Analysis: Testing" />
</form>
<form action="/teaching/CS473-Fall2022/msr/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
height:40px;" value="Mining Software Repositories" />
</form>
<form action="/teaching/CS473-Fall2022/project/">
    <input type="submit" style="background-color:firebrick;color:white;width:185px;
height:40px;" value="Reengineering Project" />
</form>

<br/>
<br/>

Target Application
==============
[LinkedIn](https://github.com/linkedin/kafka) project is a variant fork of [Apache Kafka](https://github.com/apache/kafka) running at LinkedIn. 

Kafka was born at LinkedIn. The run thousands of brokers to deliver trillions of messages per day. They run a slightly modified version of Apache Kafka trunk. The [LinkedIn](https://github.com/linkedin/kafka) variant contains the LinkedIn Kafka release.

Assignment
==========

### 1. Contextualization of the Project

[LinkedIn](https://github.com/linkedin/kafka) is a clone-and-own variant of 
[Apache Kafka]((https://github.com/apache/kafka)) that was created by copying and adapting the existing 
code of Apache Kafka that was forked on 2011-08-15T18:06:16Z. The two software systems kept on synchronizing 
their new updates until ```2021-07-06T17:39:59Z```. Since ```2021-07-06T17:39:59Z``` (divergence date), the two 
projects do not share common commits yet actively evolve in parallel. Currently, ( as of ```2022-10-01T15:01:39Z```), 
LinkedIn has 367 individual commits, and Apache Kafka has 1,216 individual commits. Development becomes 
redundant with the continued divergence, and maintenance efforts rapidly grow. For example, if a bug is 
discovered in a shared file and fixed in one variant, it is not easy to tell if it has been fixed in the 
other variant.

**General problem illustration**

The figure below illustrates clone-and-own, where variant2 (forked repository) was 
cloned-and-owned from ```variant1``` (original repository). When ```variant2``` forked by the 
developer  (```fork_date```), it inherited all commits from variant1. Then, between the 
```fork_date``` and ```divergence_date```, both variants synchronized commits, keeping both 
variants even. After the ```divergence_date```, the variants stopped synchronizing commits.

<img src="/images/473/zoomed_patch.jpeg" alt="Patch" style="width:1000px;height:406px;" align="center">

Let us assume that the developer of ```variant1``` identified a bug after the 
```divergence_date``` that is spread across files' foo, bar, and lot. The developer 
then decided to create a ```social fork``` or a ```branch``` of the source 
repository, ```patched``` the ```buggy``` files, and finally integrated the ```patch```
back into the ```main branch``` of the ```variant1``` using a ```pull request```. 
There are four possible scenarios on the ```git_head``` of ```variant2```:

1. The developer of ```variant2``` could have patched the ```buggy``` file in one 
of the previous commits before the commit at the ```git_head```. This is a case of 
**effort duplication (ED)**.
2. The file at the ```git_head``` of the target still contains the ```buggy``` lines.
This is a case of **missed opportunity (MO)**. We can even calculate how long the 
target branch has missed the patch by considering the distance between the ```patch``` 
integration date and date at the ```git_head```.
3. The file at the ```git_head``` of the target contains both the ```buggy``` and the ```patched``` 
lines. In this case both effort duplication and missed opportunity are present. 
This is a ***split case (SP)***.
4. The file at the ```git_head``` of the target does not contain any of the ```buggy``` 
or the ```patched``` lines. So this case would **not** be **interesting (NI)**.

We developed a clone detection tool, [PaReco](https://github.com/patchesandmissedmatches/patchesandmissedmatches),
that can extracts patches from any source variant ( e.g., [Apache Kafka]((https://github.com/apache/kafka)) 
in a family and classifies the patches as a MO, ED, SP, or NI in the target variant (e.g., [LinkedIn](https://github.com/linkedin/kafka)). 
The file patches.xls contains patches (MO and SP) that have been identified in the 
source variant Apache Kafka that are missing in the divergent target variant 
[LinkedIn](https://github.com/linkedin/kafka).

Your assignment is to identify numerous patches from [patches](https://docs.google.com/spreadsheets/d/1zUSgYGCOMewzpQHE2eBd0yfDv_3lBp8_6rols1yp5xo/edit#gid=0) 
that are of different sizes and integrate them in the divergent target variant 
variant LinkedIn. The size can be measured in terms of number of commits, files_changed, 
added_lines, deleted_lines.

Since the fork has diverged, it may have changed the shared file(s) present in the patch (pull request) that is applied to the 
upstream variant. Therefore, while performing the integration, you might experience merge conflicts. 
Some conflicts might be a result of refactoring operations applied during the evolution of the 
file(s) in both branches of [Apache Kafka]((https://github.com/apache/kafka)) and 
[LinkedIn](https://github.com/linkedin/kafka). In the lab on [Software Integration](/teaching/CS473-Fall2022/integration/) 
we used the tool [RefactoringsInMergeCommits](https://github.com/ualberta-smr/RefactoringsInMergeCommits) 
(that implements the tool [RefactoringMiner](https://github.com/tsantalis/RefactoringMiner/tree/intellij-psi)),
which can identify the **conflicting regions** using the following steps:
1. *Step 1: Detecting Conflicting Regions*: the tool uses the commands ```git merge``` and 
```git diff``` to determine the **conflicting regions** of a given ```merge_commit```. 
2. *Step 2: Detecting Evolutionary Changes:* the tool mines the ```git log``` to track the historical evolution (**evolutionary commits**) of a given **conflicting region** using the command ```git log -L <start><end>:file``` (```start```--start of the conflicting region, ```end```--end of the conflicting region, ```file```--file to track). 
3. *Step 3: Detecting Refactorings:*, the tool detects if there are any refactoring operations in the **evolutionary commits** by implementing the tool [RefactoringMiner](https://github.com/tsantalis/RefactoringMiner/tree/intellij-psi). 
4. *Step 4: Detecting Involved Refactorings:* the tool identifies refactoring operations affected by the evolution of conflicting region. These refactorings are called **involved refactorings** or overlapping refactorings since they are involved in the changes related to the conflicting region.

However, [RefactoringsInMergeCommits](https://github.com/ualberta-smr/RefactoringsInMergeCommits) has a dependency on an 
older version of the library of [RefactoringMiner](https://github.com/tsantalis/RefactoringMiner/tree/intellij-psi) that 
implements a few refactoring operations. You will need to extend [RefactoringsInMergeCommits](https://github.com/ualberta-smr/RefactoringsInMergeCommits)
with a few refactoring operations that you find frequently occurring in the patches (pull requests), you are going to integrate into the fork variant [LinkedIn](https://github.com/linkedin/kafka).
You will identify the frequently occuring refactorings the method we used in *Task 4* in the [Refactoring Assistants
](/teaching/CS473-Fall2022/refactoring/) lab. Remember to write tests for the newly added refactorings.

Next, [RefactoringsInMergeCommits](https://github.com/ualberta-smr/RefactoringsInMergeCommits) uses ```git merge``` and 
tries to synchronize the branches. To integrate patches (pull requests), we shall have some changes in 
*Step 1: Detecting Conflicting Regions* as follows:

  1. Fork [LinkedIn](https://github.com/linkedin/kafka)
  2. Clone the fork you have created</li>
  3. Add the remote repository that we are going to cherry-pick the patch using these two commands.

``` 
git remote add apache https://github.com/apache/kafka.git
git fetch apache
git cherry-pick merge_commit
``` 
The ```merge_commit``` is the pull request merge commit you are trying to integrate into the fork.

The next steps are the same as they are applied in [RefactoringsInMergeCommits](https://github.com/ualberta-smr/RefactoringsInMergeCommits)


After identifying the *overlapping refactorings* and the *nonoverlapping refactorings* in 
the *conflicting region*, use the knowledge of refactoring to integrate the patch into the 
variant. 

   









### 2. Getting Started Instructions
Please pay attention to the following instructions. You need to send an email to <em></em><a href="mailto:john.businge@unlv.edu">me</a> with:
* Subject "Reengineering - Linkedin". 
* Message Body:
  * The full name of the members in your group (including yours). Remember, a maximum of 3 
  people, but you are allowed to work with less than 3 if you want to.
  * Attach the **pre-conditions report** (PDF format) in your message.

* Your Pre-conditions Report should contain the following:
  * Project Name
  * Full names of all the members in your group
  * A link to your GitHub repositories (which shows you already forked [LinkedIn] and [RefactoringsInMergeCommits](https://github.com/ualberta-smr/RefactoringsInMergeCommits))
  * The members are set as collaborators to the GitHub project.
  * Invite me as collaborator on your forked repositories. (my [GitHub ID - ```johnxu21```](https://github.com/johnxu21)).
* Demonstrate the ability to build the projects. For this, we want a statement from the group 
attesting they managed to successfully build the projects. You can also attach a screenshot of 
your IDE with the project source and a message like "build successful".
* Simple Class Diagram of the class being patched (or buggy class). A simple class diagram has 
only the name of the class and its interactions with the other classes (there are two examples in 
JPacman repository in the "docs" folder). This is to reinforce your initial understanding of the 
system. You only need to focus on the classes associated with the patch and the classes that are 
called in those classes. There is no need to go deeper into the class structure (i.e., if buggy 
class calls Class X, and Class X calls Class Y, then you do not need to show Class Y since it is 
not being called directly by the buggy class). We are not going to evaluate your strictness to the 
proper UML notations, therefore focus on modeling and understanding classes interactions. 
* [Optional] A rough planning of the scope and goals. As you noticed the project refactoring is 
open to interpretation (this is on purpose). Therefore, is up to you, students, to plan the scope 
for your project. It is entirely possible for different groups to have different scopes and planned 
activities based on this assignment.

### 3. General Coding Instructions
In order to work on this assignment, the following coding/repository instructions apply:
* Fork [LinkedIn](https://github.com/linkedin/kafka), and clone the source code for your 
team to work on it. In the Documentation 
webpage, you can find further instructions on how to build the software (adapt accordingly, 
as many open source projects are a bit careless with keeping their documentation up-to-date).
* If you are working on this assignment as a group, then all members should be added to 
the repository as collaborators. 
* Commit/push your changes regularly providing information on the activity performed. 
Any "single" activity that requires file maintenance must be committed as a single commit 
with a simple description of the maintenance performed. For example, if you change the system 
to remove a God Class, your commit should be:
  * ```fixing merge conflicts on patch X, class Y  or unit tests for class Y.```
  * Another example, let's suppose you also introduced new ```tests``` along with the ```refactoring/code changes```:
  * ```refactoring Class Y to add the patch + new test added```
  * Of course, pushing the ```tests``` on a separate commit would also be acceptable (actually, it would be better to do so).
  * It is not considered good practice to commit a big chunk of modified files without providing a 
  reason that explains why those files have been modified. Therefore, try to split your commits 
  into smaller units (that may help with the grading).
  * Your GitHub commits history will be evaluated. Therefore, be sure to commit and push regularly.
  * Be sure to commit/push the final version of your reengineering project before the final 
  deadline. The final commit will be considered for evaluation as part of your assignment submission.

### 4. Development Activities 
Then, more specifically, we ask you to perform the following activities, and report about these in your project 
report:

----------
  [I. Design recovery]


<center>
<img src="/images/473/design_recovery.jpeg" alt="Design Recover" style="width:500px;height:427px;" align="center">
</center>


I. Describe the current design implementation of the selected feature in the current Software. Clearly indicate how this design is located in the architecture of the project.

----------
  [II. Redesign]

<div style="text-align: center;">
<img src="/images/473/Redesign.jpeg" alt="Redesign" style="width:450px;height:153px;" align="center">
</div>

II. Compose a generic design that describes how the new functionality / feature should be integrated 
and how the design handles the interaction with the rest of the system. It should be clear 
that the new design not only supports the new feature but also does not severely impact the 
code quality.
  
   It will be necessary to redesign the test suite in such a way that it can cope with the new feature and design.

----------
  [III. Management]

<div style="text-align: center;">
<img src="/images/473/project-management.jpeg" alt="Project Management" style="width:550px;height:275px;" align="center">
</div>

III. Estimate the effort required for (i) refactoring towards the new requirements; and (ii) changing/extending the tests.

--------
  [IV. Refactoring/ Code Change]

<div style="text-align: center;">
<img src="/images/473/refactor1.jpeg" alt="Project Management" style="width:400px;height:206px;" align="center">
</div>

IV. Refactor the current implementation of the Software such that it can handle the new feature.

   Adjust/extend the tests of the project to preserve their effectiveness and coverage during and after refactoring.

----------

* You will be required to perform a number of techniques presented during the lab sessions. These are:
  * Analyzing: Metrics and Visualization; and Mining Software Repositories.
  * Restructuring: Testing; and Refactoring; and Integration. **Please use the techniques that you 
   deem applicable to your problem. You need to convince us in the report why you decided to 
   use/exclude some techniques.**
* This project emphasizes the sound, systematic analysis of the presented problem, the associated 
solution space, and the chosen solution(s). The software reengineering sessions are composed in 
such a way as to prepare you for such a project. We stimulate you to assess the benefits and 
drawbacks of the techniques presented in the lab sessions and ask you to exploit the analysis 
techniques wisely. You are free to use alternative analysis techniques and tools as much as you 
deem necessary. 
* What concerns the refactoring-part, we emphasize the use of tests. Our minimum requirements are:
  * Determine the extent to which the current tests provide feedback on your future refactoring-steps. 
  Quantify this (i.e., show coverage information).
  * Compose an argument discussing why the tests are adequate/inadequate for your chosen 
  refactoring scenario. If inadequate, adjust the tests as needed. Be efficient with regard 
  to the time invested in testing. 

### 5. General Evaluation
To show that you have passed the assignment, you will have to demonstrate the following:

* You possess the knowledge to plan and selected the appropriate reengineering patterns for your project activities.
* You have made a selection of analysis techniques (e.g., code integration, mining software 
repositories, metrics and visualization as seen in the lab sessions, but others are allowed 
as well), and have applied these techniques in a sound, systematic manner. You have indicated 
clearly (using screenshots, results of the interpretation of the output of the techniques) 
how you have used the results of these analysis techniques.
* You have performed the above activities (decomposed into (i) Design Recovery; (ii) Redesign; 
(iii) Management; and (iv) Refactoring) and discussed them in your project report.
* The restructurings you have applied are behavior preserving:
  * You can demonstrate the mapping between each of the classes from the original structure 
  with the new structure.
  * The compilation process succeeds flawlessly.
  * The tests run without flaws and show increased testing coverage making it more reliable.
* The introduction of the new design clearly indicates the project is ready to be released in a 
language of choice. You are not supposed to carry out the refactoring process completely. 
Select and execute a set of refactorings that sufficiently illustrate your proposed solution.
* The report is written in a clear manner detailing all the steps and reasoning for the project. 
Remember that the report is the document that registers all your work. Thus, it is the most 
important artifact for the evaluation process.

For a more precise on-point view of the evaluation criteria, please look over the check-list 
(for each report) on the course's main page.

### 6. Report 
Aspects that we typically like to see addressed in the final report are:
* **Context**: Briefly discuss the context in which you are running your project (do not just 
copy verbatim the text on 1. Contextualization).
* **The Problem at Hand**: Clarify the problem at the base of the project, and indicate its 
intrinsic difficulties (again, do not just copy the assignment problem description, elaborate 
based on your chosen interpretation, goals, and scope).
* **Reengineering Patterns**: You explicitly state the patterns (from the OORP book) that you 
selected and used throughout the project.
* **Project management**: Demonstrate how you have organized the work, and how you are controlling 
it (instead of the work controlling you!)
  * **Scope**: What are the boundaries of your project? What is not included in the project?
  * **Risks**: Which risks were envisioned, and which have been mitigated? What is the priority 
  of the risks that still need to be mitigated? E.g., which external dependencies might have an 
  effect on your outcome? Which alternatives have you prepared in case this risk instantiates?
* **Software reengineering**:
  * **Tests**: How can you verify that you satisfy the requirements? Which testing strategy have 
  you selected, and what are the arguments for this selection? How confident are you that your 
  solution satisfies the requirements?
  * **Quality assurance**: What are the non-functional requirements? E.g., how do you differentiate 
  between a good and a bad solution?
  * **Refactoring**: Which refactorings did you perform on the project? Why is it better now? 
  How does your refactoring help to support the new intended features?

Those are aspects we like to see addressed/tackled/discussed/explained/presented in the Final 
Report. In the Intermediate Report, we expect less detail. However, groups that try to start 
addressing some of the above concerns most often have a better Intermediate Report. In the 
Pre-conditions Report, although not necessary, it might be better if your group starts to plan 
the scope and goals for the project.

Please also keep in mind and check the Report Guidelines and the Evaluation Checklist on the main page.

### 7. Final Remarks
If you have any questions about the project or the report, please contact me.







 





















 

















