# Ansible NodeJS App Automation Deployment

<p float="left">
  <img src="" width="100">
  <img src="" width="100">
</p>

----

## Introduction

I will be automating the installation of a NodeJS application in a cloud server (Digital Ocean). This is what I want to achieve.

1. Create a Droplet on DigitalOcean
2. Write Ansible playbook
   * Install node & npm on server
   * Copy Node artifact and unpack
   * Start application
   * Verify App is running successfully

## Playbook

I will write the playbook play for the installations of node, npm and their dependencies in the droplet. I will be aided by the documentation for instance when configuring the installation like [apt](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html), etc.

```yaml
---
- name: Install node and npm
  hosts: 209.97.186.177
  tasks:
    - name: Update apt pkg manager (repo & cache)
      apt: update_cache=yes force_apt_get=yes cache_valid_time=3600
    - name: Install nodejs and npm
      apt:
        pkg:
          - nodejs
          - npm
```

The next play to write will be for the deployment of the app. I'll use an old project of nodeJS and package it to a tar file which when unpacked will give me the **package folder** that will house the *Dockerfile, app and package.json* files.

![packing-npm](./images/image-1.png)

```yaml
- name: Deployment of nodejs application
  hosts: 209.97.186.177
  tasks:
    - name: Copy nodejs folder to a server
      copy:
        src: /Users/apple/Documents/DevOps/Projects/Ansible-NodeJS/nodejs-app/nodejs-app-1.0.0.tgz
        dest: /root/app-1.0.0.tgz
    - name: Unpack the NodeJS tar file
      unarchive:
        src: /root/app-1.0.0.tgz
        dest: /root/
        remote_src: yes
```

It took about 5 minutes and the deployment was successful.

![deployment](./images/image-2.png)

If I SSH in the server I should see the unpacked NodeJS application.

![checking-deployment](./images/image-3.png)

