# nosec is novuln? Analyzing the Bandit exceptions in Python codebases to discover vulnerable patterns

# Problem
All the popular programming languages has security analyzers that help developers identify vulnerable programming patterns. These are generally run as a part of CI to make sure vulnerable code does not go into productions. These analysis techniques are based mostly on heuristics and thus have false positives, and hence add configurations to skip some errors if developers find a rule to be such. This also comes at the cost of developers exploiting the configurations to _just pass the CI_, by adding exceptions. For example, Python's Bandit analyzer suggests adding exception when _"the line has been reviewed and the issue is a false positive or acceptable for some other reason"_.

With this project we aim to 
1. Find the programming patterns that leads to adding Bandit exceptions
2. Identify vulnerabilties if any, that were highlighed by Bandit, but were ignored.
3. Propose improvements to the design and usage of analyzer to avoid such pitfalls.

# Context
- What is static security analysis?
- Why static analysis is hard / uses heuristics?
- Python
    - Small context on how popular Python is, how it's packaging makes it easier to consume.
    - What bandit does? How is it incorporated?
    - Why is it popular? How popular is it(a metric)?
    - How bandit plugins work? One example for valid false positive? One example where we should not add exception.
    - Exceptions: CLI and inline comments

# Approach
1. To investigate how many populuar open source code bases use static security analyzers. Also to understand distribution of different security analyzers across these codebases, aiming to determine how commonly they are employed.
2. If they don't use any, we can try running some analyzer on them?
    - Why are they not running?
    - Can we get any bugs by running? Check all warnings?
    - This probably has some papers?
        - [A Large-Scale Security-Oriented Static Analysis of Python Packages in PyPI](https://ieeexplore.ieee.org/abstract/document/9647791)
1. Study how frequent are these exceptions among popular codebases.
    - Are there comments explaining why it was done.
    - Create a database of such patterns
2. Run all the `nosec` lines through _some model_ and see if it can give some summary on the patterns used.
    - Speed up the analysis process.
2. Identify if there were any vulnerabilities hidden due to these exceptions.
3. Take top n excecption patterns and study if there are safe alternatives, and if so, why are developers not using them. If not, what can the language do to improve them.
4. How can we improve these analyzers? What is state of the art?
7. Finding if there was `nosec` in the past commit and if later removed, why was it removed? Does the git commit or any comments say what happend?

# Scope and Evaluation Plan
We will be focusing of the following 3 studies under the scope for the project and will also define a set of extended scope based on the availability of time.

1. Study the popular Python open source libraries which make use of Bandit.
2. Analyze the security exceptions in these projects
3. Identifying the security impact
4. Areas of improvement for the analyzers to avoid developers exploiting the exceptions

### 1. Study the popularity of Bandit in open source Python libraries
With the help of GitHub's rich GraphQL APIs, we aim to collect data regarding various security analysis tools used in Python developers. Further narrowing down to Bandit's usage, we will userstand how Bandit is being consumed in these repositories, i.e in Continuous Integrations (CIs), git pre-commit hooks etc. This will help us narrowing down the list of repositories to run our further analysis on.

### 2. Analyze the security exceptions in these projects
With the help of [1], we will select top 1000 (approximate) repositories and analyze the exceptions in the repositories. For a given project, this will be further broken down into
a. How many security exceptions are with the CLI option (`--skip`)?
b. How many security exception are added with inline comments (`# nosec`)?
c. Which Bandit plugins lead to the most exceptions?

Both (a) and (b) can be achived by using GraphQL API, or can be performed offline by cloning the narrowed list of repositories. (c) will be performed manually in case of inline comments to identify the target plugin, or in case of CLI exceptions, can directly mapped to the plugin with the help of the exception ID. There is further scope for improving (c) by using a simple heuristic to map the exception line to plugin, by analyzing the Python AST for te given line.

At the end of this study, we should have a broader idea of most common exceptions and we should be able to classify the exceptions into broader categories to simplify the further study of identifying issues in such patterns.

### 3. Identifying the security impact
Previous work will help us narrowing down the analysis to the categories of exceptions, based on the common programming patterns. Manual analysis will be performed on these patterns and validated with the secure counterparts suggested by Bandit plugins and other language standards to identify if these exception are secure in nature. We will also work on automating this process.

This work should help us in getting a better idea about the following two scenarios
a. Are standard library APIs limiting developers to consume them securely?
b. Are developers compromising security at the cost of implementation ease?

### 4. Areas of improvement for the analyzers to avoid developers exploiting the exceptions
By now, we should have better idea on the (mis)use of the security exceptions and understand if there is scope for improvements. This will be on the questions posed in 3a and 3b. This study will help us conclude on the work, and come up with the concrete steps in make Python libraries more secure.

# Future work / Additional scope
1. Incorporate other analyzers in Python such as snyk, safety, pysa etc.
2. Analyze similar patterns across other languages (C++, C#, Java etc)

# References
- [Bandit official documentation](https://bandit.readthedocs.io/en/latest/)
- [A Large-Scale Security-Oriented Static Analysis of Python Packages in PyPI](https://ieeexplore.ieee.org/abstract/document/9647791)