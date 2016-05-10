---
title: Way to create git repository
date: 2016-05-10 15:58:52
categories: Git  
tags: [Git]
---

# Create a new repository on the command line
Use the following steps to init and push a repository in local command line  
{% codeblock %}
echo "# helloworld" >> README.md
git init
git add README.md
or git add -A
git commit -m "first commit"  
#After the git init, use the add origin to correlate the local repository to desired remote address
git remote add origin git@github.com:githubaccount/project.git
git push -u origin master
{% endcodeblock %}
	
<!--more-->
# Create a new repository by git clone the remote
Firstly, clone the remote repository and paste the codes inside the folder