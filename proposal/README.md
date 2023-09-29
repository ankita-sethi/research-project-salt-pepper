# Title: nosec is novuln? Analyzing the Bandit exceptions in Python codebases to discover vulnerable patterns

# Problem
All the popular programming languages has security analyzers that help developers identify vulnerable programming patterns. These are generally run as a part of CI to make sure vulnerable code does not go into productions. These analysis techniques are based mostly on heuristics [reference here] and thus have false positives, and hence add configurations to skip some errors if developers find a rule to be such. This also comes at the cost of developers exploiting the configurations to _just pass the CI_, by adding exceptions. For example, Python's Bandit analyzer suggests adding exception when _"the line has been reviewed and the issue is a false positive or acceptable for some other reason"_. Is this what developers actually do? Let's find out!

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
1. How much of the popular open source code bases use security analyzers.
    - Distribution
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
- Finding popularity of Bandit
- On top n repos using bandit
    - Finding how much `nosec` is used
    - Finding how huch CLI exceptions are used
- Summarizing top exception patterns
    - Understanding why developers don't use alternatives suggested by bandit
    - Can we improve bandit's false positive rate?

# Future work / Additional scope
1. Incorporate other analyzers like snyk, safety etc.
2. Analyze the pattern in the tools in various across languages (C++, C#, Java etc)