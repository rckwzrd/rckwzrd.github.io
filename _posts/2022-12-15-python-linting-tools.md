---
layout: post
lint-def: https://stackoverflow.com/a/8503586
static-analysis-def:
flake8:
mccabe:
pycodestyle:
pydocstyle:
pyflakes:
pylint:
yapf:
black:
autopep8:

---




[linting definition]({{page.lint-def}})

Linting is the process of analyzing code for syntax, structure, and style. Linting is a form of static code analysis that occurs prior to code exuction at the program level. In practice linting can occur while code is being editing, saved, or something remote pipeline.

In the python programming language the standards for code style are contained in pep8 and implemented by an ecosystem of linting tools. 

Each tool has its own opinionated interpretion of the standards and serves a unique purpose. Understanding what each tool does and how it relates to other linters can be challenging. 

The purpose of this post is to provide a short reference list on some of the main python linters. Providing a set of pointers should help disentangle conflicts between each linting tool. 

purpose, type, language, link to package, and relation to other linters
