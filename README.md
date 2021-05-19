# Debian on a Mac Mini

This project contains my personal configuration for Debian Xfce
running on low memory machines like my old Mac Mini "late 2009" with
4GB RAM and an Asus EeePC with 2GB RAM. The Mac Mini is still fast
enough for what I need, including compiling, but opening several web
pages in Firefox quickly ends up using more memory than it has.

The Ansible playbook currently configures just swaaping on zram.
Fedora already uses a similar configuration since Fedora 33. After
lots of experiments, I decided to set the size of the zram swap to
150% of the avilable RAM, similar to what Google uses in ChromeOS and
pehaps Android (not sure if this is still the case, but 150%
practically doubles the amount of RAM in the sysem if zram ca acieve a
compression ratio around 3:1). This is workload-dependent, I see
compression ratios of around 3.7:1 or 3.2:1 in most cases.

My playbook only supports Debian Buster; I plan to add support for
Bullseye after it's released. Due to a limitation in the *zram-tools*
package in Buster, you can't choose the algorithm, and you have to set
the *CORES* variable explicitly to 1, to prevent *zram-tools* from
creating as many zram disks as cores, each using a number of htreads
equal to the number of cores, which brings the system to its knees
(this seems to be fixed in Bullseye, whose version of *zram-tools*
also lets you pick the compression algorithm).

## How to use

If you aren't already unsing Ansible install it with `sudo apt install
ansible`. Now edit its inventory file to contain something like the
following, using `sudoedit /etc/ansible/hosts`:

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

You can now clone this repo and run the playbook:

```
cd /tmp
git clone https://github.com/lpancescu/debian-desktop.git
cd debian-desktop
ansible-playbook -K local.yml
```

That's all, you can now reboot the system.

