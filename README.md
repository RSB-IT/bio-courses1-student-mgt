
# VM Setup

## STEP -1: IMPORTANT: fork this repo

Fork this to a private repo, add course coordinators. this makes it possible for multiple people to admin the machine.

## Step 0: Local machine setup

- Clone your fork of this repository using git
- Install ansible (`pip install --user ansible passlib`, or follow the instructions [here](http://docs.ansible.com/ansible/intro_installation.html))
- Install ansible roles: `ansible-galaxy install oefenweb.postfix geerlingguy.certbot geerlingguy.nginx oefenweb.rstudio_server`


## Step 1: Set up VM

Get a VM up and running, either with NCI, RSB IT, or vultr or similar. Needs to run either latest debian stable or ubuntu LTS release.

## Step 2: Provision VM with ansible

This repository contains the ansible playbooks needed to set everything up. The following are needed however:

- A list of users. This should be as a CSV, saved as `userlist.csv` under this directory. The columns are `email,role` (see template), role $\in$ {student, staff}. It is converted to the format Ansible needs using a script (see below). **NB:** They must be valid emails, as they are sent a password via email. Also, you can add a column `uid` and that will be their username, otherwise it is derived from their email as `username_firstbitofdomain`, e.g.  `u1234567@anu.edu.au` -> `u1234567_anu`.
- A host list. A template is `example.hosts`. Copy this to `hosts`, and check that the IP/domain is up to date (under `ansible_ssh_host`). You need to be able to ssh to `root@HOSTNAME` with key auth, you have to set that up manually first. You also need to get the domains registerd, one for the machine, one each for jupyter and rstudio. I suggest www.xxxxxxxxx.com, jupyter.xxxxxxxx.com and rstudo.xxxxxxxxx.com.


To make life simple for multiple courses, I've split the user stuff off per course

For a new course, do:

```
$ cp example.userlist.csv userlist_$COURSE.csv
$ libreoffice --calc userlist_$COURSE.csv
$ ./scripts/users2yml -o vars/userlist_$COURSE.yml userlist_$COURSE.csv
$ cp example.dousers.yml dousers_$COURSE.yml
$ vim dousers_$COURSE.yml
# edit the welcome message, and make the vars file point to vars/userlist_$COURSE.yml
$ vim teaching.yml
# add `include: dousers_$COURSE.yml` to the end like the other ones
$ ansible-playbook -i hosts teaching.yml # initially, you shouldn't need to do this more than once
# OR
$ ansible-playbook -i hosts dousers_$COURSE.yml  # once the server has been set up, just to update the list of users & course files
```
