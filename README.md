# About 

This package contains utility scripts to manage:

* git tags: add and delete tags using (1) a canonical format and (2) some controls
* github releases: uses the Github Release API version 3

Those scripts have been initially created to manage the production release workflows of [GATB](https://github.com/GATB) and [PLAST](https://github.com/PLAST-software) software projects running on the [Jenkins Platform hosted at Inria](https://ci.inria.fr/). However, the scripts are actually of a more general use since they do not rely on these particular projects.

# Tagging a git repository with canonical release numbers

The main idea of the `git_tag_manager.sh` script aims at providing a way to easily tag a git repository using a canonical version number: "vM.m.p", that includes **M**ajor, **m**inor and **p**atch numbers.

Script to use is as follows:

    git_tag_manager.sh [-h] [-D] [-t <message>] -M <major> -m <minor> -p <patch>

So, the following command line:

    git_tag_manager.sh -M 1 -m 5 -p 12

creates a git tag "v1.5.12" on both local and remote project repositories. There is a control here: the script fails if the tag already exists.

You can also set a tag message, as follows:

    git_tag_manager.sh -M 1 -m 5 -p 12 -t "this is the new release of the software"

When argument -t is omitted, default message is: "new release".

If you want to delete a tag, simply use:

    git_tag_manager.sh -M 1 -m 5 -p 12 -D

in such a case, tag "v1.5.12" will be deleted from both local and remote project repositories. Again, there is a simple control: script fails if tag does not exist.

To move a tag from an older commit to the current one, simply run:

    git_tag_manager.sh -M 1 -m 5 -p 12 -D
    git_tag_manager.sh -M 1 -m 5 -p 12

in the row.

# Mastering Github Release API v3

Github provides an [API to manage project releases](https://developer.github.com/v3/repos/releases). This API is quite complete, but also quite complex to use for newbies. So, the following script has been setup to make Github Release API very straightforward to use.

Master script is:

     github_release_manager.sh

It includes automatically two other scripts:

     github_release_api.sh 
     json-v2.sh
     
All of these scripts have to be located in a same directory.

**Requirements** 

You need to have setup a Github token, as explained [here](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

**How to use the `github_release_manager.sh` script?**

The use of `github_release_manager.sh` relies on the required parameters:

* Credential parameters to access a remote github repository:

	  -l "login" -t "token" 

* Github repository path:
	  
	  
	  -o "owner" -r "repository" 
	
* Release identification:

	  -d "git_tab"
	
* Release management command:

	  -c "command"

**Let's take a real-life example**

In the following examples we will suppose we are working on the "plast-java-app" software repository located at https://github.com/PLAST-software/plast-java-app. 

Considering that URL, we call "PLAST-software" the *owner* and "plast-java-app" the *repository*. Then, to manage that project we need to use a *login* and a *token*, let's say "pgdurand" and "268ujk" (of course, this is a fake token).

So the basic skeleton of a `github_release_manager.sh` command is as follows:

        github_release_manager.sh \
    (1)     -l pgdurand -t 268ujk \
    (2)     -o PLAST-software -r plast-java-app \
    (3)     -d v2.3.1 \
    (4)     -c xxx

* (1): the credentials to use Github API
* (2): the targetted Github project
* (3): a release tag
* (4): xxx: a command to execute

**Use case 1: create release called "v2.3.1" on "plast-java-app" owned by "PLAST-software"**

For that, we use the "create" command:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c create

**Use case 2: uploading local file(s) to that release**    

For that, we use the "upload" command and pass in the local file path, as illustrated here:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c upload plast-java.tar.gz

It is worth noting that you can pass in several files to that command (wildcards are not allowed), *e.g.*:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c upload plast-java.tar.gz README.md

**Use case 3: list all files associated to a particular release**

For that, we use the "flist" command, as follows:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c flist
    
**Use case 4: list all releases associated to a particular project**

For that, we use the "rlist" command ("-d" argument is of course not required), as follows:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -c rlist
    
**Use case 5: display information about a particular project release**

For that, we use the "info" command, as follows:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c info
  
**Use case 6: delete a file from a particular project release**

For that, we use the "delete" command and pass in the name of the file to delete from the release, as follows:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c delete README.md

*Note:* use with caution, there is no undo!

**Use case 7: remove a particular project release**

For that, we use the "erase" command and pass in the file, as follows:

        github_release_manager.sh \
            -l pgdurand -t 268ujk \
            -o PLAST-software -r plast-java-app \
            -d v2.3.1 \
            -c erase

*Note:* use with **extreme** caution, ther is no undo!

**Getting help: "-h" argument**

	> github_release_manager.sh -h
	
	usage: [-h] [-s] -l <login> -t <token> -o <owner> -r <repository> [-d <git_tab>] [-m <message>] -c <command> [file ...]
	  
	Credential parameters used to access remote github repository:
	  -l <login> -t <token> -o <owner> -r <repository> 
	
	Release identification:
	  -d <git_tab>
	
	Release managment commands are provided using:
	  -c <command> command to execute.
	   'command' is one of create, list, upload, delete, info.
	   create: create a new release.
	    flist: list files available for an existing release.
	   upload: upload file(s) to an existing release.
	   delete: permanently delete remote file(s) from an existing release.
	     info: print out some information about an existing release.
	    erase: permanently delete an existing release. Use with extreme caution!
	    rlist: list existing releases for a repository.
	
	   All commands but 'rlist' require the -d <git_tag>.
	
	   Commands 'upload' and 'delete' expect files as remaining command line arguments:
	       -c upload file1.tgz file2.tgz
	
	Notice:
	   /!\ this script does not handle file name/path containing space characters.
	
	Other arguments:
	  -s turn script to silent mode
	  -m release message: only used when creating a new release
	  -h display this message

# Release notes

- v1.1.0 (eschen42)
  - added 'draft' command
- v1.0.0 (pdurand)
  - released Patrick Durand's code as forked from https://github.com/pgdurand/github-release-api
# License

This project includes a slightly modified version of [JSON.sh](https://github.com/dominictarr/JSON.sh) which is covered by MIT and Apache V2 licenses.

Remaining scripts are covered by the Apache V2 license.
