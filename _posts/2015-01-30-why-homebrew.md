---
layout: post
title:  Why Homebrew?

---

## Why [Homebrew?](http://brew.sh)

Seriously, Homebrew is pretty cool. It makes the process of installing and updating key packages like Ruby, Node and Git incredibly simple.

- It installs packages `/usr/local/` then symlinks them into the /bin so you don't to mess with your $PATH
- It correctly sets permissions on `/usr/local` so you don't need to `sudo ...` 
	- System Integrity Protection
	- For more info, check this out: [here](https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Installation.md#installation)
- You can use Homebrew as a version manager. Using `switch` and `link` you can manage package versions which eliminates the need for other version managers like `nvm`.

> Find more info on using Homebrew as a version manager here:
- `$ man brew`
- [How can I brew link a specific version?](http://stackoverflow.com/questions/13477363/how-can-i-brew-link-a-specific-version)
- [Switching Package Versions with Brew](http://thejacklawson.com/2012/09/switching-package-versions-with-brew/)
- [Install a Specific version of a homebrew formula](http://effectif.com/mac-os-x/installing-specific-version-of-homebrew-formula)

## Install Homebrew

XCode - Homebrew requires XCode's command line tool. You will need to either install Xcode from the App Store or download the command line tools as a separate package from the Apple developer portal [here](https://developer.apple.com/downloads/)

Simply homebrew to [install](http://brew.sh)

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

If you just installed XCode and haven't agreed to the license yet, you'll receive the following message:

	You have not agreed to the Xcode license.
	Before running the installer again please agree to the license by opening
	Xcode.app or running:
        sudo xcodebuild -license

Once you agree to the license rerun the install command above. You should see the following:

	==> Installation successful!
	==> Next steps
	Run `brew help` to get started