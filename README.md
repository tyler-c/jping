# jping [![Build Status](https://travis-ci.org/supertylerc/jping.svg?branch=master)](https://travis-ci.org/supertylerc/jping) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/supertylerc/jping/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/supertylerc/jping/?branch=master)

## About

`jping` is a [python][1] script that connects to devices, retrieves the
ARP table/cache, and pings each of the IP addresses in the cache.

## Installation

### Prerequisites

You need:

* [py-junos-eznc][2]
* [prettytable][7]

### Installing

Follow the directions below (or modify as you see fit):

```bash
mkdir $HOME/src && cd $_
git clone https://github.com/supertylerc/jping.git
mkdir $HOME/bin
ln -s $HOME/src/jping/jping.py $HOME/bin/jping
cd
```

You want to update `etc/settings.yml` to include your credentials and list of
routers.  You must include the `vendor` on the list of routers.  For now, the
only supported vendor is `juniper`.  You can add support for additional vendors.
Follow the patterns in `jping.py`, `etc/settings.yml`, and `lib/router.py`.

### Updating

I recommend that you update regularly.  I run a daily cronjob that
updates all of the repositories I use.  An example:

```bash
15 0 * * * cd /Users/tyler/src/jping && git pull --no-edit --quiet > /dev/null 2>&1
```

Of course, this only works if your machine is actually _on_ at 00:15, so
you'll need to manually update if it's not:

```bash
cd $HOME/src/jping && git pull --no-edit --quiet
```

## Usage

`jping` requires one arguments: a check type (`-c` or `--check`).  The value
passed to this argument can be either `pre` or `post`.

Example:

```bash
tyler@deathstar ~/j/jping > feature/JPING-3-object-oriented-design ⁝ ⬆ ✱
❯ ./jping.py -c pre                                                                                         [23:03:54]
>>> elapsed time 45s

tyler@deathstar ~/j/jping > feature/JPING-3-object-oriented-design ⁝ ⬆ ✱
❯ ./jping.py -c post                                                                                        [23:04:58]
+---------------+------------+---------------+----------------------+-----------------------+
|     Router    | Interface  |   IP Address  | Success on First Run | Success on Second Run |
+---------------+------------+---------------+----------------------+-----------------------+
| 192.168.0.151 | ge-0/0/0.0 |    10.0.2.2   |         True         |          True         |
| 192.168.0.151 | ge-0/0/0.0 |    10.0.2.3   |         True         |          True         |
| 192.168.0.151 | ge-0/0/2.0 |  192.168.0.7  |         True         |          True         |
| 192.168.0.151 | ge-0/0/1.0 | 192.168.0.169 |        False         |         False         |
+---------------+------------+---------------+----------------------+-----------------------+

tyler@deathstar ~/j/jping > feature/JPING-3-object-oriented-design ⁝ ⬆ ✱
❯                                                                                                           [23:05:03]
```

> Don't be too alarmed by the amount of time it took to run the pre-check.  The
> delay is actually the delay caused by the virtualized box.  On a production
> router or switch (or firewall), it would be much faster.

## Rewritten (again?!)

### v0.1.0

`jping` was initially a flexible [Perl][4] script that parsed
configuration and pinged all possible IP addresses in all configured
subnets in all configured routing-instances.

Doing things this way was expensive, and excluding routing-instances was
not exactly easy.  There's also the problem of screen-scraping.  It's
possible (however unlikely) that the output could change, breaking the
script.

### v0.2.0

For this reason, `jping` has been rewritten in Python and takes
advantage of `py-junos-eznc` to communicate with Juniper Networks
devices using [NETCONF][5].  This version has been tagged as `v0.2.0`.

### v0.3.0

Additionally, `jping` was rewritten to be slightly more Pythonic and extensible.
In addition to the changes for `v0.2.0`, the new rewrite (`v0.3.0`) returns the
ability to use `jping` as a way to automatically compare the results of a check
prior to and after a maintenance.  This functionality was missing from the
`v0.2.0` rewrite.  Although it was planned for addition, this rewrite happened
instead.

`v0.3.0` also increases the extensibility of `jping`.  It can conceivably
support additional vendors.  This can be accomplished by subclassing `Router`.
Follow the pattern used for the `Juniper` class.

## Author

Tyler Christiansen

## License

BSD 2-Clause

[1]: https://www.python.org/ "Python"
[2]: https://github.com/Juniper/py-junos-eznc "py-junos-eznc"
[3]: https://docs.python.org/2/library/getpass.html "Python getpass"
[4]: http://www.perl.org "Perl"
[5]: https://en.wikipedia.org/wiki/NETCONF "NETCONF"
[6]: https://github.com/supertylerc/jping/tree/v0.1.0 "jping Perl"
[7]: https://pypi.python.org/pypi/PrettyTable "prettytable"
