# Bash helper scripts
These are a few helper functions written in Bash that I use to help with daily tasks that are repetitive and eat up time.

If you find bugs or make updates to these scripts that would be beneficial to others, please contribute them back to the community.

## Required for your bash_profile
```
  USER="<your username>"
  DRUPAL_LOC="<path to drupal install on sandboxes"
  REPOS=(list of supported project repositories)
  REPO_HOME=<local path to where you repositories are installed>
  
  # Return the url of the sandbox
  function sandbox {
    echo "${2:-root}@<url-of-sandbox>-${1:-$USER}<url-of-sandbox>"
  }
  
  # Determine master branch by repo convention if not master
  function getMaster {
    master="master"
    
    # logic to determine the appropriate development root on which to build your branches
    [[ $(pwd | grep -i "<name of repo>") ]] && master="<name of development branch>"
    ...
    
    # return
    echo $master
  }
  
  #Load functions
  for file in $(find <local path where you keep these scripts> -type f -name "functions-*"); do
    test -f $file && . $_
  done
```

## functions-git
- `newbranch <branch-name>`: Creates a new branch off of the appropriate master branch, along with the tracking branch.
  - i.e., `$ newbranch US123-new-branch`
- `fork <branch-name>`: Creates a new branch based on the current branch you are on (as well as the tracking branch).
  - i.e., `$ fork US123-branch-fork`
- `push`: Runs the linter first if you are in webux or webrh, prompts for a commit message (or you can optionally put the message after the push command), prefixes the message with the user story for the branch, then pushes to tracking branch
  - i.e., `$ push`
  - i.e., `$ push Minor update to package.json`
- `$(currbranch)`: Provides the name of the current branch for the repo you are in.
  - i.e., `$ echo $(currbranch)`
- `gitclean`: Runs `git add .` and `git stash` to clear out any files you don't currently need
- `co <branch-name>`: Stashes uncommitted changes, checks out the appropriate version of master, checks out the branch, and pulls the latest.
  - i.e., `$ co US123-branch-to-checkout`
- `killbranch <branch-name> <branch-name>`: Removes the local and tracking branch for the provided branch(es).
  - i.e., `$ killbranch US123-branch-to-kill US124-branch-to-die`
  
## functions-dev
- Shortcuts to cd to the top level of your repositories
  - i.e., if your repository is named project: `$ project` will automatically cd you to the top level of that repo.
- `sshsandbox`: SSH's into your sandbox or can accept 1 input to log into a box that doesn't match your username.
  - i.e. `$ sshsandbox` or `$ sshsandbox <other-username>`
  
## Red Hat project-specific tasks
These will be less useful for general audiences.

### functions-webux
- `runwraith`: Still a WIP but will (in theory) kick off grunt watcher in the background and generate wraith baselines.

### functions-webrh
- `buildstatus`: This will return the build status of your sandbox; to check the status of a box other than your own, pass in that username: `$ buildstatus <other-username>`
- `buildbox`: This will kick off a build of your sandbox off of master and show you it's build progress in the command line.
- `pksync`: This bower links the webrh repository with webdrupal and rsyncs to your sandbox.  To change the sandbox it builds on, you must set the USER variable to a different value: `$ USER="<other-username>"; pksync`
- `pkimport`: This ssh's into your sandbox, cds to the location of the Drupal install, runs the pbi importer, then clears the cache. The default runs --all or you can pass in the specific patterns you want to import.
  - i.e., `$ pkimport card group band`
  - To import on a box other than your own, you must first set the USER variable to equal that username: `$ USER=<other-username>; pkimport card group band;`
- `pkbuild`: This ssh's into your sandbox, cds to the location of the Drupal install, runs a pbi remove and then the import, clearing the cache at the end.  The default runs --all or you can pass in the specific patterns you want to remove and reimport.  To import on a box other than your own, follow the same steps as the pkimport (above).
  - i.e., `$ pkbuild card group band`
- `testupdate <test names>`: This accepts a space-separated list of patterns and will create new baselines for those patterns for webrh.
