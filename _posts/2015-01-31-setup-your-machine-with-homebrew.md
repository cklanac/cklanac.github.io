---
layout: post
title: Install Git, Ruby and Node using Homebrew
---

As previously mentioned, Homebrew rocks. This post walks thru installing Homebrew and then installing Git, Ruby and Node. As well as configuring git autocompletion.

### Install Homebrew

> XCode - Homebrew requires XCode's command line tool. You will need to either install Xcode from the App Store or download the command line tools as a separate package. You can download the command link tools from the Apple developer portal [here](https://developer.apple.com/downloads/). Or wait until prompted during the install process.

Install [Homebrew](http://brew.sh)

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

Homebrew requires `xcrun` which requires Xcode command line developer tools. If you haven't install Xcode you will be prompted to install the command line tools. 

![xcrun command dialog](/img/xcrun-command-dialog.png)

If you have installed XCode and haven't agreed to the license yet, you'll receive the following message:

	You have not agreed to the Xcode license.
	Before running the installer again please agree to the license by opening
	Xcode.app or running:
    	sudo xcodebuild -license

![Xcode Commnad Line Tools License Agreement](/img/xcode-commnad-line-tools-license-agreement.png)

Once you agree to the license rerun the install command above. You should see the following:

	==> Installation successful!
	==> Next steps
	Run `brew help` to get started

Check the `/usr/local` permissions.

![Usr/Local Permissions](/img/usr-local-permissions.png)

### Install Git

Check to see if git is installed and which version

	which git
	/usr/bin/git
	git --version
	git version 2.4.9 (Apple Git-60)

The above is installed with OSX. Install our own version using Homebrew `brew install git`

	brew install git
    ==> Downloading https://homebrew.bintray.com/bottles/git-2.6.2.el_capitan.bottle
    ######################################################################## 100.0%
    ==> Pouring git-2.6.2.el_capitan.bottle.tar.gz
    ==> Caveats
    The OS X keychain credential helper has been installed to:
      /usr/local/bin/git-credential-osxkeychain

    The "contrib" directory has been installed to:
      /usr/local/share/git-core/contrib

    Bash completion has been installed to:
      /usr/local/etc/bash_completion.d

    zsh completion has been installed to:
      /usr/local/share/zsh/site-functions
    ==> Summary
        /usr/local/Cellar/git/2.6.2: 1398 files, 33M

Then double check that it is properly linked:

    which git
    /usr/local/bin/git

    git --version
    git version 2.6.2

> Note: Homebrew packages are more recent than the pre-installed version, but not always up-to-date with the latest.

#### Configure Git

Add your name and email address.

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

Setup credential helper so you can use https repo without entering your password every time.

	git config --global credential.helper osxkeychain

Next time you checkout a private repo or push to a public repo you'll be asked for your password which will be set in Keychain Access.

#### Enable Git completion

> Note the Caveats in the output when we installed Git above

	The OS X keychain credential helper has been installed to:
	  /usr/local/bin/git-credential-osxkeychain

	The "contrib" directory has been installed to:
	  /usr/local/share/git-core/contrib
	
	Bash completion has been installed to:
	  /usr/local/etc/bash_completion.d

	zsh completion has been installed to:
	  /usr/local/share/zsh/site-functions

You can enable Bash Completion by running the following which adds a line to your `.bash_profile` to source in the file.

	echo "source /usr/local/etc/bash_completion.d/git-completion.bash" >> ~/.bash_profile


Or better still, we can use this [suggestion](http://superuser.com/questions/31744/how-to-get-git-completion-bash-to-work-on-mac-os-x/300298#300298). Add the following which checks if the file exists then sources it. 

	if [ -f `brew --prefix`/etc/bash_completion.d/git-completion.bash ]; then
	    . `brew --prefix`/etc/bash_completion.d/git-completion.bash
	fi

> Note: the 'dot operator' is just a shortcut for 'source'

Verify that it works. Open a new terminal window or `source ~/.bash_profile`.

	git co<tab><tab>
	commit   config

### Install Ruby

First let's check which version of ruby you're running.

	which ruby
	/usr/bin/ruby
	ruby --v
	ruby 2.0.0p481 (2014-05-08 revision 45883) [universal.x86_64-darwin14]

The version listed above comes with OSX but using it causes permission problems and can cause conflicts down the road. So, let's install our own version.

	brew install ruby
	ruby -v
	ruby 2.3.0p0 (2015-12-25 revision 53290) [x86_64-darwin15]

Great, now we have out own version of ruby properly installed and symlinked to `/usr/local/bin/`.

> You can still use `rbenv` is manage ruby versions, if you'd like

	brew install rbenv 
	echo 'eval "$(rbenv init -)"'  >> ~/.bash_profile 
	source ~/.bash_profile
	echo $PATH
	which ruby
	/Users/Chris/.rbenv/shims/ruby

### Install Node

	brew install node
	which node
	/usr/local/bin/node
	node --version
	v4.1.1

#### Manage Node versions with Homebrew instead of `nvm`

Check the current node version

	node --version
	v4.1.1

Unlink the current node. Basically removes the symlink from the `/usr/loca/bin` directory	

	brew unlink node

Search for the available node Homebrew formulae 

	brew search node
	leafnode	    node (installed)	nodebrew	    nodenv
	homebrew/versions/node010		Caskroom/cask/node-profiler
	homebrew/versions/node012		Caskroom/cask/node
	homebrew/versions/node04		Caskroom/cask/nodebox
	homebrew/versions/node06		Caskroom/cask/nodeclipse
	homebrew/versions/node08		Caskroom/cask/soundnode

Install the desired version

	brew install homebrew/versions/node012
	node --version
	v0.12.7
