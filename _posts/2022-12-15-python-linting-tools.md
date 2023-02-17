---
layout: post
lint-def: https://en.wikipedia.org/wiki/Lint_(software) 
static-def: https://en.wikipedia.org/wiki/Static_program_analysis 
flake8: https://pypi.org/project/flake8/
mccabe:	https://pypi.org/project/mccabe/
pycodestyle: https://pypi.org/project/pycodestyle/
pydocstyle: https://pypi.org/project/pydocstyle/ 
pyflakes: https://pypi.org/project/pyflakes/
pylint: https://pypi.org/project/pylint/
yapf: https://pypi.org/project/yapf/
black: https://pypi.org/project/black/
autopep8: https://pypi.org/project/autopep8/ 
pep8: https://peps.python.org/pep-0008/

---

[Linting]({{page.lint-def}}) is the process of analyzing code for syntax, structure, and style errors. Linting is a form of [static code analysis]({{static-def}}) that occurs prior to code exuction at the program level. In practice linting can occur while code is edited, saved, or evaluated in a development workflow. The goal of linting is to minimize runtime errors and ensure code quality. 

In Python linting standards are documented in the [PEP8 style guide]({{pep8}}) and implemented by an ecosystem of linting tools. Each tool has an opinionated interpretation of the standards and serves a specific role in the linting process. Understanding what each tool does and its relationship to other tools can be challenging.

The purpose of this post is to provide a short reference for a set of common Python linters. These pointers will highlight intended usage, frame dependancies, and give a link to project documentation.

## Linting Tools

[autopep8]({{autopep8}})

[black]({{black}})

[flake8]({{flake8}})

[mccabe]({{mccabe}})

[pycodestyle]({{pycodestyle}})

[pydocstyle]({{pydocstyle}})

[pyflakes]({{pyflakes}})

[pylint]({{pylint}})

[yapf]({{yapf}})

