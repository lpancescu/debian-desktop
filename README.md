# Debian on a Mac Mini

This project contains my personal configuration for Debian Xfce
running on low memory machines like my old Mac Mini "late 2009" with
4GB RAM and an Asus EeePC with 2GB RAM. The Mac Mini is still fast
enough for what I need, including compiling, but opening several web
pages in Firefox quickly ends up using more memory than it has.

The Ansible playbook currently configures just swaping on zram.
Fedora already uses a similar configuration since Fedora 33. I saw a
compression ratio of 2.2:1 using lz4 and 2.33:1 using lzo, so setting
the size of the zram disk to 100% of the system RAM should be enough
to prevent it from using more than 50% of the system memory.

My playbook supports Debian Buster and Bullseye. Due to a limitation
in the *zram-tools* package in Buster, you can't choose the algorithm,
and you have to set the *CORES* variable explicitly to 1, to prevent
*zram-tools* from creating as many zram disks as cores, each using a
number of htreads equal to the number of cores, which brings the
system to its knees (this was fixed in Bullseye, whose version of
*zram-tools* also lets you pick the compression algorithm).

## How to use

First install Ansible and Git via `sudo apt install ansible git`. Now
use `sudoedit /etc/ansible/hosts` to add something like the following
to your Ansible inventory file:

```yaml
all:
  hosts:
    localhost:
      ansible_connection: local
  children:
    low_memory:
      hosts:
        localhost:
```

Now clone this repo and run the playbook:

```
cd /tmp
git clone https://github.com/lpancescu/debian-desktop.git
cd debian-desktop
ansible-playbook -K local.yml
```

The changes will take effect the next time you boot the system.

