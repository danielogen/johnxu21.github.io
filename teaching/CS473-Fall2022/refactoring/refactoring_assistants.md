---
layout: page
title: Refactoring Assistants
permalink: /teaching/Software-Reengineering/refactoring/
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
    <input type="submit" style="background-color:firebrick;color:white;width:185px;
height:40px;" value="Refactoring Assistants" />
</form>
<form action="/teaching/Software-Reengineering/dynamic/">
    <input type="submit" style="background-color:cornflowerblue;color:white;width:185px;
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

In this session, we will learn about some different tools available for refactoring assistance. 
By using tools, we can plan ahead the refactorings activities that could improve our design. 
We will search for bad smells (symptoms of design problems) that give us hints on where and how 
to refactor. When planning refactoring activities, keep in mind the pattern "**Keep it Simple**" 
(OORP, p.37), as it a common mistake for people to over-complicate the design of a refactored 
artifact. Another important pattern to remember when refactorings is “**Most Valuable First**” 
(OORP, p.29), as in you should prioritize the refactorings that bring more benefits first.

Despite the benefits, in some contexts, refactoring is perceived as change noise, which makes more 
difficult the completion of various software evolution related tasks. For instance, refactoring 
operations can cause merge conflicts when merging development branches (we shall look this in detail in 
Lab of [software integration](/teaching/Software-Reengineering/integration/)). 
They may distract developers when reviewing behavior-altering changes, make bug-inducing analysis algorithms
to erroneously flag behavior-preserving changes (i.e., refactorings) as bug-introducing, 
cause breaking changes to clients of libraries and frameworks, cause unnecessary test executions 
for behavior-preserving changes.

During this session, we will perform simple refactoring tasks. However, it is important to remember 
that for the Course we focus on Strategic Refactoring, i.e., we should refactor with a clear 
reason or goal in mind. 

<br/>

Lecture sides 
======
* [here](../../../files/Refactoring_Assistants.pdf)

Sample Project
========
* [django CMS](https://github.com/django-cms/django-cms)

Materials & Tools
========
* IDE-  [PyCharm](https://www.jetbrains.com/pycharm/) 
* [CodeScene](https://codescene.com/) - **no** installation necessary, but it requires a GitHub account. This tool integration with GitHub allows it to visualize your repositories. The Technical Debt part show refactoring targets. The Code Biomarkers show a more detailed analysis of smells but it is only available to paid subscribers.
* [SonarQube](https://www.sonarqube.org/) is a tool/platform that performs static analysis on source codes. Download the free community edition.

<br/>

Setup / Preparation - (Artifacts that need refactoring)
=============
* Be sure to follow the setup from the first session ([Metrics and Visualization Lab Session](/teaching/Software-Reengineering/metrics/)) 
* Also if have not already, download the book for this course "[Object-Oriented Reengineering Patterns](http://scg.unibe.ch/download/oorp/)" (_Note: OORP, p.xx refers to a page in the pdf version of this book_)
* **Note:** You are only required to **plan** the refactoring for this lab. Implementation is optional.

Task 1: Django-CMS on CodeScene
=========
For our first task, we are going to use CodeScene to suggest which artifacts are in need of refactoring.

To do this, select the "**Code**" menu on the left side, and then the "**Hotspots**" submenu.  
In this visualization, the hotspots are artifacts with a lot of commit activities (i.e., they change a lot during the software evolution and maintenance).  
On that visualization, you can check the tab on **Refactoring Targets**. Look at the recommended refactoring targets.

If you select a specific file in this visualisation (or the hotspots visualisation), on the right, it will display
more details. When you scroll down to the details section, you can see a few actions. They include Review, 
Source code, and X-ray. Check out these options and see for yourself what information CodeScene can provide.

Also, notice that, for some files, CodeScene highlights other coupled files. Explore these code couplings as well.

> _As you perform this task, keep in mind the pattern **"Most Valuable First" (OORP, p.29)** — prioritize refactoring opportunities that offer the highest benefit or reduce the most risk._
  
> _You should also consider the pattern **"Keep It Simple" (OORP, p.37)** — avoid the temptation to over-engineer when identifying or planning a refactoring._

> _Finally, apply **"Study the Exceptional Entities" (OORP, p.107)** — CodeScene helps reveal outliers in complexity and change frequency that often signal deeper design problems._

**Questions:**
* Did CodeScene visualization help you identify possible targets for refactoring?
* Did CodeScene give you hints or clues on how to refactor the proposed targets?
* Did CodeScene help you visualise the extent of the refactoring activity?
<br/>

Task 2: Django-CMS on SonarQube
============
For the second task, we will use a more complex and dedicated tool to find refactoring targets. 
Follow the instructions in the [documentation](https://docs.sonarsource.com/sonarqube-server/latest/try-out-sonarqube/)
to install and run SonarQube. You may either install it locally or run it in a Docker container. **Important:** It is recommended  and generally easiest installtion option for Mac M-series users to use [Docker](https://docs.docker.com/desktop/setup/install/mac-install/) instead of the "From the zip file" option. If you face any issue, please reach out to the TAs for help.

If you are successful, you should be able to run an analysis of your local clone of Django-CMS.

Click on the "Code Smells" and analyse the detected smells. You should see that SonarQube also explains 
the smells ("Why is this an issue?"). 

> _Use the pattern **"Split Up God Class" (OORP, p.263)** when you encounter very large classes that take on too many responsibilities.  
> These classes often become maintenance bottlenecks and are prone to error. SonarQube may highlight them through complexity, duplication, or high coupling._

> _If the code is hard to follow or understand, apply the pattern **"Refactor to Understand" (OORP, p.127)**.  
> Small, safe refactorings like Rename Method or Extract Method can improve clarity and help you reason about the system’s structure before attempting more extensive changes._

> _When unsure about the implications of a smell, use **"Step Through the Execution" (OORP, p.133)**.  
> Walking through how the code behaves at runtime can help validate whether a static smell points to a deeper design issue._

**Questions:**
* Did SonarQube help you identify specific design problems (code smells) that tools like CodeScene did not?
* Which smells stood out to you as most critical to address, and why?
* Were there any cases where the smell detection might have been misleading or not worth acting on?

Task 3: Django-CMS Strategic Refactoring
========
For the Reengineering Course, we value the concept of Strategic Refactoring, which is refactoring with a goal. 
Tools can help identify artefacts with smells that could lead to potential issues. 
However, only a developer can identify the necessary artefacts for a specific goal. Let's do that for Django-CMS. 

Browse through the [issue tracker](https://github.com/django-cms/django-cms/issues) of Django-CMS and search for issues with the following keywords “**is:open label:"kind: enhancement"**”. 
You will notice there are some issues which require a patch.

Choose a few issues and plan the necessary refactoring task(s) to support them. You can start with the simplest 
refactoring tasks to avoid "breaking" the code or go big according to the pattern "**Most Valuable First**". 
There is no wrong path. Do whichever you find easier or most logical for you.

> _If the area of the code you're planning to change is hard to understand, consider applying the pattern **"Refactor to Understand" (OORP, p.127)**.  
> This allows you to use safe refactorings (like renaming or extracting methods) to gain clarity before deeper changes._

> _You may also apply **"Expose the Design" (OORP, p.141)** — refactor parts of the system to better reflect their architectural role,  
> making it easier to support new responsibilities in line with the enhancement issue._

Questions:
1.	What were your strategies and reengineering patterns for planning this refactoring? 
2.	Why did you consider these refactoring tasks important for your goal?
3.	Did the previous tools (CodeScene or SonarQube) identify the refactoring targets you deemed necessary for this goal?
4.	Do you prefer to refactor to improve code quality or to refactor with a goal?

Optional Task 1: Django-CMS Strategic Refactoring (Part 2)
===========

To complete this optional task, you should implement the planned refactoring tasks. 
Remember to ensure that your refactoring change is not breaking the application.

Optional Task 2: Duplicate Code Detection on SonarQube
==========

For this optional task, let's remind ourselves of the previous lab session on Duplicate Code. 
If you looked over the SonarQube analysis on Django-CMS, you may have noticed this tool also 
detects duplicated code. Check out how SonarQube presents the duplicated code it found.

Did you like SonarQube visualisation of duplicated code snippets? Or do you prefer another tool? 
Why do you like your chosen tool?

Additional Reading Material
=============

In case you want to dive deeper into refactoring, here are some extra materials about it.

1. M. Fowler, K. Beck, J. Brant, W. Opdyke, and D. Roberts. Refactoring: Improving the Design of Existing Code. Object Technology Series. Addison-Wesley, 1 edition, June 1999.
2. M. Lanza and R. Marinescu. Object-Oriented Metrics in Practice - Using Software Metrics to Characterize, Evaluate, and Improve the Design of Object-Oriented Systems. Springer, 2006.
3. M. Fowler and J. Kerievsky. Smells to refactorings quick reference guide. reference sheet, 2005.: http://www.industriallogic.com/blog/smells-to-refactorings-cheatsheet/
4. F. Khomh, M. D. Penta, Y.-G. Guéhéneuc, and G. Antoniol. An exploratory study of the impact of antipatterns on class change- and fault-proneness. Empirical Softw. Engg., 17(3):243–275, June 2012. http://link.springer.com/article/10.1007%2Fs10664-011-9171-y
5. Nikolaos Tsantalis, IEEE, Ameya Ketkar, and Danny Dig, [Refactoring 2.0](https://users.encs.concordia.ca/~nikolaos/publications/TSE_2020.pdf). 2020

Post-Lab Quiz: Refactoring Assistants
==========
Posted on WebCampus