---
date: 2026-07-26
categories:
  - Release
tags:
  - 1.4.0
---

# RecoveryBox v1.4.0 Release

RecoveryBox v1.4.0 is about to be released. This version brings new features and significant improvements to RecoveryBox.

I took the time to completely review the installation script's operation, as well as the technology used to deploy services. The project has moved from a pure Bash deployment to a hybrid approach combining Bash and Ansible. This change comes with a shift in philosophy regarding how services are deployed, with the goal of making RecoveryBox more flexible and easier to maintain.

Version 1.4.0 also marks the launch of the website you're currently reading. Although still a bit austere, it will expand over time.


<!-- more -->

## Ansible {: .rb-section-title}

### What is Ansible? {: .rb-section-subtitle}

Ansible is a deployment and configuration automation tool. It allows you to deploy services on remote machines in a simple and efficient way. Its task-based operation allows validation that each deployment step completed successfully. If an error occurs, Ansible stops the deployment and displays an error message. It's a tool I use daily in my work, and it seemed natural to completely refactor the installer to use it.

To be honest, the installer code was clean but was becoming increasingly complex to maintain. Each component was managed by a function, each function had its own execution conditions and dependencies, and everything was in a single file. This architecture works well for a small script, but `recovery_box_install.sh` had reached 925 lines. I spent my time scrolling or using `Ctrl+F` to find my way around.

### Integration {: .rb-section-subtitle}

The philosophy I apply with Ansible is quite simple. There's an installation playbook named `Install.yml`, which itself calls the `recoverybox` role and specifically its `main.yml` task. This task then calls other task files based on conditions linked to variables (we'll come back to this). Each task file therefore contains a list of actions to perform, grouped by subject.

To illustrate, here's an excerpt from the `main.yml` file of the `recoverybox` role:

??? note "main.yml"
    ```yaml
    - name: Install Access Point
      ansible.builtin.import_tasks: install_access_point.yml
      tags:
        - install_access_point
        - dry_run

    - name: Install Apache2
      ansible.builtin.import_tasks: install_apache2.yml
      tags:
        - install_apache2
        - dry_run
      when: recoverybox_enable_apache

    - name: Install rb-library
      ansible.builtin.import_tasks: install_rb_library.yml
      tags:
        - install_library
        - dry_run
      when:
        - recoverybox_enable_library
        - recoverybox_enable_apache
    ```

And here's an excerpt from the `install_apache2.yml` file:

??? note "install_apache2.yml"
    ```yaml
    - name: Install Apache2
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Enable proxy modules
      ansible.builtin.command:
        cmd: "a2enmod {{ item }}"
      changed_when: false
      loop:
        - proxy
        - proxy_http
        - proxy_wstunnel
        - rewrite

    - name: Create /data/www directory
      ansible.builtin.file:
        path: /data/www
        state: directory
        owner: root
        group: root
        mode: '0755'
    ```

As explained previously, Ansible uses variables to create conditions. But in reality, it uses variables for everything. Everything can be parameterized, allowing for dynamic actions during execution. You can decide whether to run a particular task, but also populate a file with values defined beforehand, or even defined on the fly following previous actions. All variables have a default value located in the `defaults/main.yml` file of the `recoverybox` role. These variables can be overridden by files. This is what the new `RecoveryBox_install.sh` script uses.

Passing everything through variables also makes updates easier to manage. Each module (or nearly) has a variable specifying its version. Update management is therefore simplified. Technically, it will be enough to modify the variable's value for the module to be updated on the next run of the installation script. It's also possible to perform partial updates by modifying only certain variables. This allows updating one module without touching the others.

Passing everything through variables also allows enabling or disabling a module based on a variable's value. I was therefore able to modify `rbstatus` as well as the `http://recovery.box` homepage so that displayed services are dynamic based on enabled modules. If you disable the `kiwix` module, it will no longer appear in the homepage or in `rbstatus` and will no longer be accessible via the web interface. This allows customizing RecoveryBox to your needs.

### What about the installation script? {: .rb-section-subtitle}

`RecoveryBox_install.sh`, in addition to launching the Ansible playbook, creates the variable file `/etc/recoverybox/custom_config.yml` from the questionnaire presented to the user. This overrides part of the `recoverybox` role's default variables. It's still possible to manually edit this file to act on other variables not managed by the installation script. Typically, if you want to modify Wi-Fi channels or the access point password, you'll need to do it manually in this file. You can also use it to download other ZIM files for Kiwix, or only enable the English Wikipedia, or only versions with images. In short, it's a configuration file that allows customizing RecoveryBox at will, and a system administrator could even bypass the installation script entirely and manage everything via Ansible (network configuration and the switch to systemd-networkd will still need to be done beforehand).

### Industrialization {: .rb-section-subtitle}

The benefit of using Ansible is also the ability to industrialize RecoveryBox deployment and updates. It's possible to deploy RecoveryBox on multiple machines simultaneously via a third-party machine that will execute actions on target machines. The logic is quite simple: you take a RecoveryBox or a server that will serve as the deployment machine, install Ansible on it, and configure an inventory file with target machines. This inventory is a file that can be very simple, with just each machine's IP, username, and password. This way, you can deploy RecoveryBox on multiple machines at once and update them with a single command. It's a considerable time-saver for system administrators managing multiple RecoveryBox instances.

But you can push it even further with the inventory. You can customize each machine's configuration via the inventory, essentially bringing `custom_config.yml` into the inventory. So you can, with a single command, deploy a machine that only acts as an access point and serves documents, while simultaneously deploying another machine with Croatian Wikipedia and the Croatia map in Kiwix, and yet another machine whose hotspot has been customized to work with Wi-Fi 6 and a different SSID name.

At present, this serves me strictly no purpose, let's be honest. I wanted to design things so that deployment and maintenance could be easily industrialized, because I had in mind that this project might interest associations, NGOs, or even services like civil protection, which are often deployed at events requiring centralized information and communications. But for now, I have no feedback on this point. If you're interested in this kind of use case, feel free to contact me so we can discuss it.

## The Website {: .rb-section-title}

With the arrival of version 1.4.0, I integrated the wiki directly into the project. Previously, I used GitHub's wiki, but I didn't find it pleasant to use. Layout options are too limited and the rendering isn't great. The main blocking point for me was that the wiki, to make this offline solution work, was... online. The wiki's utility was close to zero (not to mention it wasn't complete, documentation is always the most annoying part to do). So I decided to integrate it directly into the project and make it accessible via the RecoveryBox web interface.

But this posed a new problem. The advantage of having a wiki on GitHub is that it can be consulted by anyone before downloading the project. It's hard to follow the project's quickstart when you have to go read Markdown files in the repo. So I decided to buy my domain name and put the wiki online. But a wiki alone on a website isn't very sexy. So I decided to build a complete website with a homepage, a blog page, and the wiki. The site is still under development but is already functional.

The website, like the wiki, is built with MkDocs Material. It's a static site generator that lets you create websites from Markdown files. I'm discovering the tool as I go along based on my needs. It's quite practical for making static sites without too much fuss. I wanted a solution that was easy to implement and update, and that would let me easily reintegrate the original wiki's Markdown files. Web development isn't really my strong suit; I'm clearly more comfortable with Bash, Python, or a switch's terminal, so I rely heavily on AI, especially for customizing the design. I still take the time to review and try to understand what the agent did, but I couldn't say if it's super optimized. But it works, and that's all I ask.

Currently, the website is hosted at OVH. When you buy a domain name from them, they offer free "web hosting". We're talking 100 MB of storage, which isn't exactly generous, but it's perfect for a showcase site or a piece of wiki as long as there aren't too many photos. For now, it does the job. I'll probably add photos to illustrate articles or screenshots in the wiki, but we'll see when the time comes. Since everything is stored in Git repos, it's super easy to redeploy the site if needed. And with GitHub Actions, the wiki updates automatically when I push changes (I should talk about that someday).

In the meantime, at the time of writing, v1.4.0 isn't out yet. I still have the service activation part to integrate into the Ansible section, and especially a full deployment to test on a fresh machine. But I wanted to tell you about this version anyway because it's quite important in the project's evolution. It's a deep overhaul of my working method and the background mechanics. I hope you'll appreciate the changes and find the website useful. Feel free to give me feedback, suggestions, or constructive criticism, it's always appreciated.