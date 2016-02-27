---
layout: post
title: Github Flavored Ruby for Github-Pages

---

## A little background

While setting up my local Jekyll blog environment I ran across the following on the Github-pages Gem repo.

> Although Jekyll itself may be compatible with prior or future versions of Ruby, different execution environments yield different results. Ruby 1.8.7 parses YAML differently than 1.9.3, for example, and Kramdown has trouble processing mailto links prior to 1.9.3. In order to ensure that building locally consistently results in the same build as what appears when published, it's essential that Ruby itself is versioned along side the Gem, despite no known incompatibilities.

This seems pretty explicit about using a particular Ruby build. Which is in contrast to the instructions on [Using Jekyll with Pages](https://help.github.com/articles/using-jekyll-with-pages/). So here’s my take on how to install Github-Pages Flavored Jekyll environment.

## Prereqs

* [Homebrew]({% post_url 2015-01-31-setup-your-machine-with-homebrew %})
* [Jekyll GitHub-Pages site]({% post_url 2015-06-21-github-flavored-ruby-for-pages %})

### Install and config rbenv and ruby-build

	brew install rbenv ruby-build

The output instructions say to run `rbenv init`. And the output from that command says to append the following to your `~/.bash_profile`:

	eval "$(rbenv init -)"

Reload your `.bash_profile`:

	source ~/.bash_profile
	echo $PATH

You should see `/Users/<username>/.rbenv/shims:` prepended to your PATH

### Install and config Github Flavored Ruby

Install the [ruby-build-github](https://github.com/parkr/ruby-build-github) plugin. The plugin is basically a set of custom definitions to inform rbenv where to find the Github Flavored Rubies.

	git clone git://github.com/parkr/ruby-build-github.git "$(rbenv root)/plugins/ruby-build-github"

Check the [GitHub Pages Dependency versions](https://pages.github.com/versions/) for the correct Github's Ruby version


    rbenv install 2.1.7-github

Create a `.ruby-version` file in your Jekyll github.io project.

    cd <path-to-your-project>
    rbenv local 2.1.7-github
    ...
    Cloning https://github.com/github/ruby.git...
    Installing ruby-2.1.7-github...
    Installed ruby-2.1.7-github to /Users/Chris/.rbenv/versions/2.1.7-github

Now verify that the correct version of Ruby is running.

	ruby -v
	2.1.7-github

Compare the output of `ruby -v` with the version showing on [here](https://pages.github.com/versions/)




