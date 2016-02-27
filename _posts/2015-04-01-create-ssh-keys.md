---
layout: post
title:  Create SSH Keys

---

Eventually you will need to create SSH keys for a remote terminal, SFTP or a hosted service. The SSH keys are the recommended way to login to a SSH server. Besides providing authentication for the Secure Shell itself, the keys are also used for SFTP and SCP, and used for passwordless login for services like GitHub and BitBucket

### Note on GiHub Recommendations:
> GitHub now recommends HTTPS URLs instead of SSH, probably because of corporate firewall and such. If you're look for passwordless logins for HTTPS URLs then checkout the Credential Helper. See: [Which remote URL should I use?](https://help.github.com/articles/which-remote-url-should-i-use/). However, GitHub suggests [Using SSH agent forwarding](https://developer.github.com/guides/using-ssh-agent-forwarding/) when working on a server. So, their recommendation seems to be... use HTTPS URLs with Credential Helper for day-to-day work your local machine and use SSH URLs for remote server operations.

Create SSH Keys

Check `~/.ssh` directory if keys already exist. 

    ls -al ~/.ssh


Create ssh keys Simple way, defaults to RSA 2048 bits for SSH protocol 2.

    ssh-keygen
    
Or use Github's recommendations.

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

* `-t` option specifies the type of key to create. Possible values `[-t dsa | ecdsa | ed25519 | rsa | rsa1]`. If invoked without any arguments, ssh-keygen will generate an RSA key for use in SSH protocol 2 connections. 
* `-b` options specifies the number of bits. According to the man pages... "Specifies the number of bits in the key to create. For RSA keys, the minimum size is 1024 bits and the default is 2048 bits. Generally, 2048 bits is considered sufficient."

* `-C` option provides a comment. The GitHub instructions imply that you need to enter the email address for you GitHub account, but the `-C` is just a comment.
  
Keep default location:

    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/Chris/.ssh/id_rsa):

Enter passphrase:

    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again:


Copy the **public** key. 
This command copies the key to the clipboard so you can easily paste it into service like GitHub or Bitbucket. 

    pbcopy < ~/.ssh/id_rsa.pub

This command installs the key to a remote host
  the Simple way;

    ssh-copy-id -i ~/.ssh/id_rsa.pub <remotehost.com>

Login into remote shell

    ssh username@<remotehost.com>

> Tip: create a shortcut
There are 2 ways to create a SSH shortcut
1. Use `alias` the `~/.bash_profile`
`alias shortname='ssh username@<remotehost.com>'`
Then login using just `shortname`
2. In the `~/.ssh/config` file
```Host shortname
    HostName remotehost.com
    User username
```
Then login using just `ssh shortname`


### References
* http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man1/ssh-keygen.1
* https://help.github.com/articles/generating-ssh-keys/
* https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets
* https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server
* http://manpages.ubuntu.com/manpages/precise/man1/ssh-copy-id.1.html
* https://help.github.com/articles/creating-an-access-token-for-command-line-use/

