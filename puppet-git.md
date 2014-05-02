Puppet. Git. You think it would be easier. 

<!--more-->

Cloning a git repo using Puppet is harder than you think. The following is the work flow I experienced trying to get a git repo cloned. Keep in mind, I am new to Puppet, so this may help those in the same boat. 

Currently using [librarian-puppet](https://github.com/rodjek/librarian-puppet) Ruby gem to download the Puppet modules. To deploy and test the Puppet modules I used [Vagrant](http://www.vagrantup.com/), the virtual instance developer environment manager. 

## Were is the docs?

Started with [puppetlabs/git](https://forge.puppetlabs.com/puppetlabs/git), though hit a road block because there was no visible examples or documentation. [Here is the best they can come up with](https://github.com/puppetlabs/puppetlabs-git/). I suppose there may be some documentation somewhere, just not easy to find.


## Invalid resource type git::repo

Next up was New Zealand eScience Infrastructure's Puppet module called [ Puppet Git](https://github.com/nesi/puppet-git). Here is the PuppetFile entry.

	# mod 'nesi/puppet-git',
	#  :git => 'git://github.com/nesi/puppet-git.git'  

Here is the implementation for cloning the `django_sample` Django web app to the folder `/var/www/django_sample`.  

    class{ 'git':
      svn => 'installed',
      gui => 'installed',
    }

    git::repo{'django_sample':
     path   => '/var/www/django_sample',
     source => 'git@github.com:cevaris/django-sample.git'
    }
    
So when executing `vagrant provision`, you get the following error.

	Puppet::Parser::AST::Resource failed with error ArgumentError: Invalid resource type git::repo at /tmp/vagrant-puppet-2/manifests/init.pp:16 on node alpha.com
	
Whats going on? Apparently the module is broken in new Puppet solutions. This is conveniently stated on their GitHub README.

	WARNING Changes to how Puppet sets up the environment variables with exec resources means this module no longer works as intended when managing repositories as any user other than root. It is recommended that the Puppetlabs vcsrepo module is used manage repositories.
	



## Puppet Module `vcsrepo`

So, as per Puppet Git's warning, the next and final Puppet module [`vcsrepo`](https://github.com/puppetlabs/puppetlabs-vcsrepo) got me what I wanted. Though, not without a hiccup. 

Here is the Librian Puppet module definition.

	mod 'puppetlabs/puppetlabs-vcsrepo',
	 :git => 'git://github.com/puppetlabs/puppetlabs-vcsrepo.git'

Here is the implementation of `vcsrepo`.

	vcsrepo { '/var/www/django_sample':
	  ensure   => present,
	  provider => git,
	  source   => 'git@github.com:cevaris/django-sample.git',
	}

So, the hiccup when provisioning the web node. 

	err: /Stage[main]//Vcsrepo[/var/www/django_sample]/ensure: change from absent to present failed: Execution of '/usr/bin/git clone git@github.com:cevaris/django-sample.git /var/www/django_sample' returned 128: Cloning into '/var/www/django_sample'...
	Host key verification failed.
	fatal: The remote end hung up unexpectedly
	
Apparently, when connecting to GitHub using the git repo SSH URI `git@github.com:cevaris/django-sample.git`, would ask for some kind of SSH authentication. This of course, was not ideal. So, to fix, simply replace the SSH URI `git@github.com:cevaris/django-sample.git` with the GitHub HTTP URI `https://github.com/cevaris/django-sample`. Like so..

	vcsrepo { '/var/www/django_sample':
	  ensure   => present,
	  provider => git,
	  source   => 'https://github.com/cevaris/django-sample',
	}

Then all is good. We are able to clone the remote git repo to the directory `/var/www/django_sample/`.

	vagrant@alpha:~$ ls -l /var/www/django_sample/
	total 12
	-rw-r--r-- 1 root root  212 May  1 22:44 README.md
	-rw-r--r-- 1 root root  123 May  1 22:44 requirements.txt
	drwxr-xr-x 4 root root 4096 May  1 22:44 sample
	vagrant@alpha:~$ 
	
The only weird thing about `vcsrepo` is that the documentation is not readily available on the front page README. Nope, you have to click into the proper source repository custom README. So....

* So for git 
	* [https://github.com/puppetlabs/puppetlabs-vcsrepo/blob/master/README.GIT.markdown](https://github.com/puppetlabs/puppetlabs-vcsrepo/blob/master/README.GIT.markdown)
* For Mercurial
	* [https://github.com/puppetlabs/puppetlabs-vcsrepo/blob/master/README.HG.markdown](https://github.com/puppetlabs/puppetlabs-vcsrepo/blob/master/README.HG.markdown)
	
