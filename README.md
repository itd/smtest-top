# smtest-top

submodule test top level

ref: https://git-scm.com/book/en/v2/Git-Tools-Submodules

## set up some git config options to make thing happen automagically

    git config --global submodule.recurse true

or, do this:

    git config --global alias.pullall '!git pull && git submodule update --init --recursive'


## Clone the initial top-level repo

    git clone git@github.com:itd/smtest-top.git

## set up the submodule

    git submodule add  https://github.com/itd/itd.vidbase roles/itd.vidbase

## See what happened

    $ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

      new file:   .gitmodules
      new file:   roles/itd.vidbase


## check-in .gitmodules and commit
    git add .gitmodules 
    git commit -m"commit gitmodules file"
    git push

## Checking for changes in submodules
Since submodules don't get tracked at the top-level, you can keep an eye 
on those with `git diff`, but with a tweak to your local git config:

    git config --global diff.submodule log 

Then a `git diff` will do the right thing.

## Clone the repo in a new location

     mkdir ../testpull
     cd ../testpull/
     git clone git@github.com:itd/smtest-top.git
     cd smtest-top/
     ls -la roles/itd.vidbase/
     git status

### clone the repo and get it all in one line

     git clone --recurse-submodule  git@github.com:itd/smtest-top.git
     git clone git@github.com:itd/smtest-top.git; cd smtest-top; git submodule init; git submodule update


### Two ways to get submodule data loaded

    cd roles/itd.vidbase
    git submodule init 

...to initialize your local configuration file, and 

    git submodule update 

...to fetch all the data from that project

This is a bit of a hassle. You *can* do it all in one command.

    git clone --recurse-submodules git@github.com/itd/smtest-top.git

## Day to day work

Submodules update. You can go into the one at a time and update them:

    cd roles/itd.vidbase
    git fetch
    git merge

**...or at the top level:**
*this will get all the submodule updates at once:

    git submodule update --remote

This will checkout the `master` branch of each submodule.

## specific submodule branch
If you need to check out a specific branch of a submodule:
    git config -f .gitmodules submodule.itd.vidbase.branch stable

Then check it all with:

    git status

If you set the configuration setting `status.submodulesummary` so Git will also show you a short summary of changes in submodules.

    git config status.submodule.summary 1
    git status

Git tries to update **all** submodules when you run 

    git submodule update --remote 

You can also update submodules individually go into each submodule dir and update them individually.

## Working on submodules

    git submodule update --remote --merge
    git push --recurse-submodules=check
    git push --recurse-submodules=on-demand

  

# Run pre-install ansible plays

A hacky workaround to a specific issue I'm having may be to create 
a pre-install playbook that runs locally to ensure everything is set up.

It might look like this:

    ansible-playbook -b --limit=somegroup_or_host ./pre-install.yml


    ---
    # pre-install.yml
    - name: "run some playbook tasks on the localhost"
      hosts: 127.0.0.1
      connection: local
      become: yes
      tasks:
       - name: "pull the git repo:
         git:
            repo: 'https://github.com/openstack/ansible-hardening.git'
            dest: ./roles/ansible-hardening
            #version: stable/rocky

