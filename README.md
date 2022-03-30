# Debian on a Mac Mini

This project contains my personal configuration for Debian Xfce
running on low memory machines like my old Mac Mini "late 2009" with
4GB RAM and an Asus EeePC with 2GB RAM. The Mac Mini is still fast
enough for what I need, including compiling, but opening several web
pages in Firefox quickly ends up using more memory than it has.

The Ansible playbook currently configures swaping on zram if the system
has less than 16GiB of RAM.  Fedora already uses a similar configuration
since Fedora 33. The kernel uses lzo-rle by default, which is a pretty
good compromise between speed and compression ratio.

My playbook supports Debian Buster and Bullseye. Due to a limitation
in the *zram-tools* package in Buster, you can't choose the algorithm,
and you have to set the *CORES* variable explicitly to 1. This prevents
*zram-tools* from creating as many zram disks as cores, each using a
number of threads equal to the number of cores, which brings the
system to its knees (this was fixed in Bullseye, whose version of
*zram-tools* also lets you pick the compression algorithm).

## How to use

After you install Ansible and Git via `sudo apt install ansible git`,
clone this repo:

```
cd /tmp
git clone https://github.com/lpancescu/debian-desktop.git
cd debian-desktop
ansible-playbook -K local.yml
```

You can change the _zram_use_threshold_mb_  variable in the _hosts_ file
to the amount of memory you consider enough to not need zram. The
default is 16384, so zrm is not configured if you have more than 16GB
RAM. You can also delete this line altogether if you don't want zram.

You can also adapt the _install_pkgs_ variable to list any additional
packages you would like to install (or remove the variable altogether if
you don't need to install anything).

```yaml
all:
  hosts:
    localhost:
      ansible_connection: local
  vars:
    zram_use_threshold_mb: 16384
    install_pkgs:
      - needrestart
      - vim
```

You can now run the playbook:

```
ansible-playbook -i hosts -K local.yml
```

The changes will take effect the next time you boot the system.

