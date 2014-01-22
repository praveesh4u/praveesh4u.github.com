---
layout: post
title: "Setting Up Octopress"
date: 2014-01-22 23:02:07 +0530
comments: true
categories: 
---

<b>Step by step instruction<b>

1. clone Source branch to praveesh4u@github.com
    	
		$ git clone -b source git@github.com:praveesh4u/praveesh4u.github.com.git

2. clone Master branch to praveesh4u@github.com/ _deploy
    
    	$ cd praveesh4u@github.com
    
    	$ git clone  git@github.com:praveesh4u/praveesh4u.github.com.git _deploy

3. If these steps are already done, do the following
		
		$ cd praveesh4u@github.com  
		$ git pull origin source
		$ cd praveesh4u@github.com/_deploy 
		$ git pull origin master
4. Next, run 
		
		$ gem install bundler
		$ bundle install
		$ rake setup_github_pages
 When prompted, enter the repository's address- 	
 		
		git@github.com:praveesh4u/praveesh4u@github.com
5. Configure git global parameters like username and email address using 		
		
		$ git config --global user.name "Praveesh A- Linux" 
		$ git config --global user.email "praveesh4u@gmail.com"
6. Add/Edit posts
7. Generate the post
		
		$ rake generate
8. Preview the generated post using 	
		
		$ rake preview 
To view the preview, go to localhost:4000
9. Commit the changes 
		
		$ git add .
		$ git commit -a -m "Commit message"
		$ git pull origin source (to sync with the server)
	 	$ git push origin source
13. Publish the post
		
		$ rake deploy
14. Done!!
