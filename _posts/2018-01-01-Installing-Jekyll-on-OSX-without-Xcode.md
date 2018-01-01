---
mathjax: false
title: Installing Jekyll on OSX without Xcode
---
Using Jekyll is awesome! Actually this whole blog is based on it. Installing it on OSX and getting it to work, however, is another story. Officially you need to install Xcode and the Command-Line Tools it ships with. Since I never use Xcode, an alternative for installing Jekyll is as follows with the Ruby Virtual Machine (RVM):

	$ xcode-select --install
	$ \curl -sSL https://get.rvm.io | bash -s stable
	$ source ~/.rvm/scripts/rvm
	$ rvm install ruby --latest
	$ gem install jekyll
	
These commands make sure you get the latest version of Ruby which ensures compatibility with the latest version of jekyll.