---
layout: post
lint-def: https://en.wikipedia.org/wiki/Lint_(software) 
static-def: https://en.wikipedia.org/wiki/Static_program_analysis 
complex-def: https://en.wikipedia.org/wiki/Cyclomatic_complexity
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
pep257: https://peps.python.org/pep-0257/

---

[Linting]({{page.lint-def}}) is the process of analyzing code for syntax, structure, and style errors. Linting is a form of [static code analysis]({{page.static-def}}) that occurs prior to code exuction at the program level. In practice linting can occur while code is edited, saved, or evaluated in a development workflow. The goal of linting is to minimize runtime errors and ensure code quality. Linters can be ran in a terminal and often integrate with text editors and IDEs.

In Python linting standards are documented in the [PEP8 style guide]({{page.ep8}}) and implemented by an ecosystem of linting tools. Each tool has an opinionated interpretation of the standards and serves a specific role in the linting process. Understanding what each tool does and its relationship to other tools can be challenging.

The purpose of this post is to provide a short reference for a set of common Python linters. These pointers will highlight intended usage, frame dependancies, and give a link to project documentation. Many of these tools appear to do the same things.

## Linting Tools

[autopep8]({{page.autopep8}})

- linter and automatic Python code formatter
- direct implementation of PEP8
- built on top `pycodestyle`
- runs from command line 
- integrates with text editor

[black]({{page.black}})

- linter and automatic Python code formatter
- strict implementation of PEP8
- limited configuration options by design 
- runs from command line
- integrates with text editor

[flake8]({{page.flake8}})

- pure linting tool with static error codes
- direct impelmentation of PEP8
- integrates `pyflakes`, `pycodestyle`, and `mccabe`
- runs from command line
- integrates with text editor

[mccabe]({{page.mccabe}})

- module that serves as a plugin for other linters
- computes and returns a graph of [code complexity]({{page.complex-def}})
- returned a graph represents paths in code
- paths are used by linters to evaluate code
- runs from command line

[pycodestyle]({{page.pycodestyle}})

- pure linting tool with static error codes
- direct implementation of PEP8
- one of the original Python linters
- often used by other linters
- runs from command line

[pydocstyle]({{page.pydocstyle}})

- pure linting tool with a focus on docstrings
- direct implementation of [PEP257]({{page.pep257}})
- PEP257 provides guidance on doc string formatting
- often paired with `pycodestyle`
- runs from commman line

[pyflakes]({{page.pyflakes}})

- simple linting tool that focuses on static errors
- limited implementation of PEP8
- does not check style convention, only errors 
- often used by other linters
- runs from command line

[pylint]({{page.pylint}})

- complex linting tool with many features
- direct implementation of PEP8 with configuration options
- provides static errors, style errors, and refactoring suggestions
- can be extended with plugins for specific frameworks
- runs from command line and integrates with text editor

[yapf]({{page.yapf}})

- automatic Python formatter
- configurable implementation of PEP8
- intended to format code to project defined style
- newer project that is still in alpha
- runs from command line

