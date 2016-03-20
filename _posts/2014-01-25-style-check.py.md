Title: style-check.py
Date: 2014-01-25 17:16
Category: Writing
Tags: style-check, python, sublime text
Slug: style-check

Writing readable scientific papers is hard, especially for beginners. Although books ([e.g., The Elements of Style](http://www.amazon.com/The-Elements-Style-Fourth-Edition/dp/020530902X)) and guides are written to promote the best practices, it is still desired that such guides can be automatically applied to our writing in the form of concrete suggestions.


[style-check.rb](http://www.cs.umd.edu/~nspring/software/style-check-readme.html) is such a commandline tool to check the grammar and style problems in Latex files. It comes with a predefined set of rules and uses regular expression to find the problems with your writing. It is so useful for me that I am wondering if it can be integrated into my favorate text editor - Sublime Text.

I rewrote style-check.rb with python and created a Sublime Text package [style-check](https://github.com/chrishan/style-check). Hope you find it useful.