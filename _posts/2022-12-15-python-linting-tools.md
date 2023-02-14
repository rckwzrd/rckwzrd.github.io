---
layout: post
lint-def: https://stackoverflow.com/a/8503586

---

working on setting up a python language server in nvim. one of the first things i do is disable all of the inline and automatic linting and code checking. this is just my preference, but all of the flags and warnings really through me off when i am cranking out some code. 

I am using the open source pylsp language server and i had to spend bit of time disabling all of the loud linters and format checkers. all i want is autocompletion, goto definitions, and hover documentation. 

Now pylsp is very modular, but I was shocked at the number of ways there are to lint, check, and format code in Python. I spent more time figuring what each tool was and how it related to other linters. below are some notes on these tools, there purpose, and relationship to others. finally, i will give a quick snippet of my init.lua showing the pylsp configuraiton i use to stiffle real time linting and style checking.

[linting definition]({{page.lint-def}})
