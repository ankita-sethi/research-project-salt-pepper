# nosec is novuln? Analyzing the Bandit exceptions in Python codebases to discover vulnerable patterns

# Problem
All the popular programming languages has security analyzers that help developers identify vulnerable programming patterns. These are generally run as a part of CI to make sure vulnerable code does not go into productions. These analysis techniques are based mostly on heuristics and thus have false positives, and hence add configurations to skip some errors if developers find a rule to be such. This also comes at the cost of developers exploiting the configurations to _just pass the CI_, by adding exceptions. For example, Python's Bandit analyzer suggests adding exception when _"the line has been reviewed and the issue is a false positive or acceptable for some other reason"_.

With this project we aim to 
1. Find the programming patterns that leads to adding Bandit exceptions.
2. Identify vulnerabilities if any, that were highlighted by Bandit, but were ignored.
3. Propose improvements to the design and usage of analyzer to avoid such pitfalls.

# Context
**Static security analysis** is a technique for identifying software security flaws without executing the program. It examines the source code for design flaws, security vulnerabilities, and coding errors that might lead to security breaches. This approach relies on heuristics due to complex code, dynamic behavior, and limited information, making statistical analysis challenging. Consequently, it may generate false positives or false negatives. 
**Bandit** is often used in security testing workflows for Python applications. Python's appeal lies in its readability and simplicity, catering to developers of all levels. Tools like pip and PyPI simplify Python packaging, easing the installation and integration of tools like Bandit. PyPI serves as a central hub for Python packages, providing a wealth of resources. Bandit's Python-centric approach efficiently identifies coding issues and security flaws, making it popular in security testing due to its user-friendly interface, seamless Python integration, and strong Python community support. Remarkably, the PyPI package "bandit" has received 5,467 stars on GitHub, reflecting its widespread adoption.
Bandit's flexibility comes through plugins, enabling users to create custom security checks. For example, developers can disable Bandit's default check B101, which detects hard-coded passwords, by adding comments. However, this may lead to valid false positives if passwords are securely managed elsewhere. Overusing exceptions in critical security checks may result in overlooking real security vulnerabilities.

Inline comments can be used to disable specific checks for a particular piece of code. For example:
pwd = "ABCDE"  # bandit:disable [B101] 
You use the command line to execute Bandit with various options and arguments to perform security analysis on your Python code. 
[B101]
exclude = path/ABCDE/File/PythonFile.py
This can be used when a developer knows that a particular piece of code is secure and should not trigger a warning.

# Approach
1. To investigate how many popular open source code bases use static security analyzers. Also, to understand distribution of different security analyzers across these codebases, aiming to determine how commonly they are employed.
2. Identify codebases that do not employ security analyzers and ascertain the reasons behind this decision. Proceed to execute security analyzer scans on these codebases to identify potential vulnerabilities or warnings. Conduct a comprehensive literature review to explore previous studies conducted in this domain like [A Large-Scale Security-Oriented Static Analysis of Python Packages in PyPI](https://ieeexplore.ieee.org/abstract/document/9647791)
3. Conduct a comprehensive investigation on a large scale to identify code lines within popular codebases that have been designated to bypass testing against security vulnerabilities. This research aims to determine the frequency of such occurrences. Generate a dataset that catalogs these exceptions for further analysis and reference.
4. Examine all lines labeled as `#nosec` or `--skip` to investigate the possibility of categorizing them into distinct clusters while providing accompanying justifications.
5. Identify if there were any vulnerabilities hidden due to these exceptions.Analyze the most prevalent N exception patterns and explore secure alternatives for these, investigate the reasons behind developers not adopting these alternatives. if such alternatives do not exist, consider potential enhancements that can be made to the programming language?
6. Identify the presence of the `#nosec` label in previous commits and summarize the reasons behind its removal, if applicable. Examine Git commits and associated comments for insights into the circumstance of its removal.

# Scope and Evaluation Plan
We will be focusing of the following 3 studies under the scope for the project and will also define a set of extended scope based on the availability of time.

1. Study the popular Python open source libraries which make use of Bandit.
2. Analyze the security exceptions in these projects
3. Identifying the security impact
4. Areas of improvement for the analyzers to avoid developers exploiting the exceptions

### 1. Study the popularity of Bandit in open source Python libraries
With the help of GitHub's rich GraphQL APIs, we aim to collect data regarding various security analysis tools used in Python developers. Further narrowing down to Bandit's usage, we will understand how Bandit is being consumed in these repositories, i.e, in Continuous Integrations (CIs), git pre-commit hooks etc. This will help in narrowing down the list of repositories to run our further analysis on.

### 2. Analyze the security exceptions in these projects
With the help of [1], we will select top 1000 (approximate) repositories and analyze the exceptions in the repositories. For a given project, this will be further broken down into :

a. How many security exceptions are with the CLI option (`--skip`)?

b. How many security exception are added with inline comments (`# nosec`)?

c. Which Bandit plugins lead to the most exceptions?

Both (a) and (b) can be achieved by using GraphQL API, or can be performed offline by cloning the narrowed list of repositories. (c) will be performed manually in case of inline comments to identify the target plugin, or in case of CLI exceptions, can directly map to the plugin with the help of the exception ID. There is further scope for improving (c) by using a simple heuristic to map the exception line to plugin, by analyzing the Python AST for te given line.

At the end of this study, we should have a broader idea of most common exceptions, and then we should be able to classify the exceptions into broader categories to simplify the further study of identifying issues in such patterns.

### 3. Identifying the security impact
Previous work will help us in narrowing down the analysis to the categories of exceptions, based on the common programming patterns. Manual analysis will be performed on these patterns and validated with the secure counterparts suggested by Bandit plugins and other language standards to identify if these exception are secure in nature. We will also work on automating this process.

This work should help us in getting a better idea about the following two scenarios:

a. Are standard library APIs limiting developers to consume them securely?

b. Are developers compromising security at the cost of implementation ease?

### 4. Areas of improvement for the analyzers to avoid developers exploiting the exceptions
By now, we should have better idea on the (mis)use of the security exceptions and understand if there is scope for improvements. This will be on the questions posed in 3a and 3b. This study will help us conclude on the work, and come up with the concrete steps in make Python libraries more secure.

# Future work / Additional scope
1. Incorporate other analyzers in Python such as snyk, safety, pysa etc.
2. Analyze similar patterns across other languages (C++, C#, Java, etc.)

# References
- [Bandit official documentation](https://bandit.readthedocs.io/en/latest/)
- [A Large-Scale Security-Oriented Static Analysis of Python Packages in PyPI](https://ieeexplore.ieee.org/abstract/document/9647791)