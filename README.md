# sympa-archive-list
A script to move the old closed [SYMPA](http://sympa.org) mailing lists into offsite storage

This script moves SYMPA mailing lists data that have been closed out of the SYMPA server, to free space.
The lists are archived as a comoressed tar file (listname.tgz) of their `expl/listname` and `wwsarchive/listname@listdomains` directories. The date of the .tgz file is set to the date the list was closed, for easier reference.
Archives can be restored later by uncopressing the .tgz file in the sympa directory (E.g. `/var/lib/sympa`), and re-activating them via the sympa admin web interface.

## Usage
```sympa-archive-list [options] [lists...]

Archives all the lists (list names, without the @... domain part) in arguments.

Options:
   -d D   destination: where to copy archives, used as a rsync destination
          e.g: D can be a: dir, host:dir, login@host:dir ...
          Mandatory. Can also be defined as a \"destination\" shell variable in
          the config file /etc/sympa-archive-list.conf
   -c C   reads also the C file as config after /etc/sympa-archive-list.conf
   -a     archive all closed lists
   -o N   for -a, archive only list closed for more than N (number) days
   -s S   uses S for the sympa directory instead of /var/lib/sympa
          this directory should contain the subdirectories expl and wwsarchive
   -n     only create archive, do not remove the files from SYMPA
   -l     only show lists that would be archived, but perfom no actual action
   -v     verbose
```
## Quickstart
1. Copy the `sympa-archive-list` somewhere on your SYMPA server. E.g. in `/usr/local/bin`
2. Create a `/etc/sympa-archive-list.conf` and add a line in it telling where to copy archives:
   `destination=user@host:/path/to/archives`
3. Test it works by a `sympa-archive-list -l -a`
4. Add it to the root corntab to run daily. E.g, to archive all lists closed for more than a month:
   `01 01 * * * /usr/local/bin/sympa-archive-list -a -o 31`
   
## License
(c) 2018 Colas Nahaboo, colas@nahaboo.net
MIT License: Open source with no strings attached.
