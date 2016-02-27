---
layout: post
title: Obligatory Jekyll Install and Setup Post

---

Obligatory Jekyll Install and Setup Post
=======================

Apparently, everyone that creates a Jekyll blog is then obliged to also create a how to install Jekyll post. It seems to be the thing to do. 

The post combines instructions found on the following pages. And, as far as I can tell, it is the "proper" way to install Jekyll. 

* [Jekyll GitHub Pages](https://jekyllrb.com/docs/github-pages/)
* [Setting up your Pages site locally with Jekyll](https://help.github.com/articles/using-jekyll-with-pages/)
* [GitHub Pages Ruby Gem](https://github.com/github/pages-gem)
* [rbenv](https://github.com/rbenv/rbenv)
* [Ruby Build Compile and install Ruby](https://github.com/rbenv/ruby-build)
* [ruby-build-github](https://github.com/parkr/ruby-build-github)
* [Dependency versions](https://pages.github.com/versions/)

### Install Homebrew

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"    

While setting up my local Jekyll blog environment I ran across the following directions on the Github-pages Gem repo.

> Although Jekyll itself may be compatible with prior or future versions of Ruby, different execution environments yield different results. Ruby 1.8.7 parses YAML differently than 1.9.3, for example, and Kramdown has trouble processing mailto links prior to 1.9.3. In order to ensure that building locally consistently results in the same build as what appears when published, it's essential that Ruby itself is versioned along side the Gem, despite no known incompatibilities.

This seems pretty explicit about using a particular Ruby build. Which is in contrast to the instructions on [Using Jekyll with Pages](https://help.github.com/articles/using-jekyll-with-pages/). So here’s my take on how to install Github-Pages Flavored Jekyll environment.

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

### Create Repo on Github and Clone to local

Create a clean repo on Github and name it `<username>.github.io`

* Create a Github repo name `<username>.github.io`
* Create a Readme file, if you’d like
* Select a License 
* Note: You don’t need to create a .gitignore. Jekyll will create one for you.

Clone to local machine

    git clone https://github.com/<username>/<username>.github.io.git

### Install GitHub Pages Gems

Create a Gemfile in your local clone of `<username>.github.io` 

	cd <username>.github.io/
	touch Gemfile
	nano Gemfile

And paste the following

    source 'https://rubygems.org'
    gem 'github-pages'

Install bundler 

	gem install bundler

Then `bundle install` the dependencies. This will install Jekyll and all the Github-Pages specific dependencies. 

	cd <path-to-your-project>
	bundle install

### Scaffold basic Jekyll site

The local <username>.github.io repo already has files like the Readme and License. So Jekyll will complain that the directory is not empty when you run `jekyll new ./`. Simply add the -f flag to force the install.

	jekyll new -f ./

Serve up the site

    jekyll serve --watch

Open site in your browser: [http://localhost:4000](http://localhost:4000)
    
### Commit and Push

    git status
    git add .
    git status
    git commit -m 'clean Jekyll install'
    git push origin master
    
### Improve the .gitignore

* Go to: https://www.gitignore.io
* Add OSX configs to .gitignore

### Customize site configs

* Open `_config.yml`
* Set title, subtitle, description etc

### Enable GitHub Flavored Markdown for Kramdown

Add the following to the `_config.yml`
 
    kramdown:
      input: GFM # http://kramdown.gettalong.org/parser/gfm.html

### Setup TravisCI

Login or create an account on [https://travis-ci.org/](https://travis-ci.org/). Enable the build on each commit.
 
![Travisci Enable Build](/img/travisci-enable-build.png)

Create a `.travis.yml` in the project root and paste the following instructions

    language: ruby
    script: "bundle exec jekyll build"

Add the following to the `_config.yml` _

    exclude: [vendor]

### Updating Github-pages
To keep Jekyll and it Github-page up-to-date, run:

    bundle update github-pages
    



