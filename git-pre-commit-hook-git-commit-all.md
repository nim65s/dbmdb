title: Git pre-commit hook & git commit --all
date: 2013-11-05
author: Nim
sites: saurel.me
category: Informatique
tags: git, scripts

<p>It's generally good when a task you have to do many times a day is really fast. For example, when I want to make a commit, I just type <code>gc &lt;message&gt;</code>, because I made <code>gc</code> an alias for <code>git commit -am</code>. And when you have to commit, it's hard to go faster.</p>
<p>But sometimes, fast is not enough.</p>
<p>Today, I just tried to add a pre-commit hook that checks pep8 for the python files I&rsquo;m about to commit. Easy.</p>
<p>The man page says that pre-commit is bypassed when we use the option --no-verify, so everything should be OK.</p>
<p><strong>BUT</strong> sadly <code>git commit -a</code> also bypasses the pre-commit hook. And I don't wanna loose my time adding manually all the modified files in the repo. <code>git add *</code> is definitively<strong>&nbsp;not</strong> a solution.</p>
<p>However, the solution is quite easy: let the pre-commit script do the right <code>git add</code> stuff !</p>
<p>By "the right stuff", I mean <code>git update-index --add</code>, because it seems that on some systems with some versions of git, git add in a pre-commit adds files but for the next commit&hellip;&nbsp;</p>
<p>Here is my implementation of this solution:&nbsp;<a href="https://github.com/nim65s/scripts/blob/master/pre-commit">https://github.com/nim65s/scripts/blob/master/pre-commit</a></p>
<p>Now, you may want to add this pre-commit to all your git repositories in every computer you use&hellip; Well, just look at this one&nbsp;<a href="https://github.com/nim65s/scripts/blob/master/pre-commit-deploy.sh">https://github.com/nim65s/scripts/blob/master/pre-commit-deploy.sh</a>&nbsp;(it just takes a <code>~/.gitrepos</code> which contains all the repositories you use on the current computer&hellip; ie the config file for this script&nbsp;<a href="https://github.com/nim65s/scripts/blob/master/gitup.sh">https://github.com/nim65s/scripts/blob/master/gitup.sh</a>)</p>
