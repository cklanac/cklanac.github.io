---
layout: post
title: Github Flavored Ruby for Pages

---

Github Flavored Ruby for Pages
=======================

## A little background

While setting up my local Jekyll blog environment [here](URL) I ran across the following directions on the Github-pages Gem repo.

	Although Jekyll itself may be compatible with prior or future versions of Ruby, different execution environments yield different results. Ruby 1.8.7 parses YAML differently than 1.9.3, for example, and Kramdown has trouble processing mailto links prior to 1.9.3. In order to ensure that building locally consistently results in the same build as what appears when published, it's essential that Ruby itself is versioned along side the Gem, despite no known incompatibilities.


This seems pretty explicit about using a particular Ruby build. Which is in contrast to the instructions on [Using Jekyll with Pages](https://help.github.com/articles/using-jekyll-with-pages/). So here’s my take on how to install Github-Pages Flavored Jekyll environment.

## Overview
We’ll install `rbenv` and `ruby-build` 

## Prereqs

	* Homebrew
	* Local <username>.github.io project running Jekyll

## Install
### Install and config rbenv and ruby-build

	brew install rbenv ruby-build

Following the instructions in the output, we’ll append the following to your `~/.bash_profile`:

	eval "$(rbenv init -)"
	
And reload your `.bash_profile`:

	source ~/.bash_profile
	echo $PATH
	
You should see `/Users/<username>/.rbenv/shims:` prepended to your PATH


### Install and config Github Flavored Ruby
First install the [ruby-build-github](https://github.com/parkr/ruby-build-github) plugin. The plugin is basically a set of custom definitions to inform rbenv where to find the Github Flavored Rubies.

	git clone git://github.com/parkr/ruby-build-github.git "$(rbenv root)/plugins/ruby-build-github"


Install Github's Ruby version

	rbenv install 2.1.0-github

Create a `.ruby-version` file in your Jekyll github.io project. 

	cd <path-to-your-project>
	rbenv local 2.1.0-github

Now verify that the correct version of Ruby is running.
	
	ruby -v
	2.1.7-github

Compare the output of `ruby -v` with the version showing on [here](https://pages.github.com/versions/)

> Side Note. So I’m not terribly familiar with the ins-and-outs of `rbenv`. But apparently it supports semver and runs the latest patch version despite the `.ruby-version`. So `ruby -v` will return the latest version (e.g. `2.1.7-github`).





