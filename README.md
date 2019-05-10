# gemoc-studio-eclipse-integration
Integration via git modules of GEMOC components @Eclipse for use by continuous integration server

## Main build:

The jenkinsfile is run as a multibranch pipeline here: https://ci.eclipse.org/gemoc/job/gemoc-studio-integration/


## Synchronization build

A job is also in charge of creating branches from submodules branches so that the CI will always have a branch
in the root repository tracking the branches in the submodules. 
https://ci.inria.fr/gemoc/job/git-sync-submodules-branches_gemoc-studio-eclipse-integration/

It uses https://github.com/dvojtise/git-sync

A simple name base pattern is used. If a git submodule doesn't have a branch for a given name, then it uses the 
master branch for this git submodule 


