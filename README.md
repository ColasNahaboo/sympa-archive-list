# sympa-archive-list
A script to move old [SYMPA](http://sympa.org) mailing lists into offsite storage. Bash script working on linux/unix servers.

This script moves SYMPA mailing lists data that have been closed out of the SYMPA server, to free space.
The lists are archived as a compressed tar file (listname.tgz) of their `expl/listname` and `wwsarchive/listname@listdomains` directories. The date of the .tgz file is set to the date the list was closed, for easier reference.
Archives can be restored later by uncompressing the .tgz file in the sympa directory (E.g. `/var/lib/sympa`), and re-activating them via the sympa admin web interface.

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
4. Add it to the root crontab to run daily. E.g, to archive all lists closed for more than a month:  
    `01 01 * * * /usr/local/bin/sympa-archive-list -a -o 31`  

## License
(c) 2018 Colas Nahaboo, colas@nahaboo.net
MIT License: Open source with no strings attached.

## Caveats, Requirements
*  Requires scp
*  Only tested on Linux Debian

## Legacy: sympa-archives-expire

I also include here the previous incarnation (published on 2009-03-14 at https://colas.nahaboo.net/Code/SympaArchiveExpire) of this script, `sympa-archives-expire` for reference. Its use is not recommended since it has not been used in versions of SYMPA more recent than the v3.

`sympa-archives-expire` is a bash script to remove [SYMPA](http://www.sympa.org/) mailing lists old archives based on expiration dates. SYMPA is a very  nice mailing list system, which provide nice web archives by using [MHonarc](http://www.mhonarc.org/). To keep things manageable, it actually sets up a separate MHonarc  repository per month. SYMPA provides a way to make subscribers expire,  but nothing is none for the archives. This is not a problem in general,  as emails consume little space, but can be problematic for lists used to send a lot of attachments (think of MS Office documents like  powerpoints), or automated build or test reports or big logs.



This script has a granularity of one month: it can only remove one month at a time. It works by being run daily under the `sympa` login in a crontab (e.g.: `01 01 * * * /usr/local/bin/sympa-archives-expire` ), without arguments. By default it do not expire lists, it just reads the file `/etc/sympa-archives-expire` to find the name of archives to expire, one per line, optionally  followed by the number of full months to keep. Full usage is obtained by ruinning it with the argument `-?` :



```
# sympa-archives-expire -?
sympa-archives-expire [options] arguments ...
reads a config file (default: /etc/sympa-archives-expire) and removes old 
archives for the listed SYMPA mailing lists.
See https://%HTTP_HOST%/Code/SympaArchiveExpire

The file should list the SYMPA mailing list archives to expire after N months
in the form:
  listname@hostname N
N is optional (default 1) and mean the number of full month archives to keep
  e.g. if N=2, the current month and the full two months before will be
  kept, older ones will be deleted
lists not listed in this file never expire
empty lines and lines beginning with # are ignored

The file should be run under the sympa account, once a day

Options:
  -c FILE   Uses FILE instead of /etc/sympa-archives-expire
  -d DIR    Sympa installation dir, defaults to /home/sympa
  -n        Do not perform cleanups, just list what it would have done
```
