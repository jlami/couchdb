# Apache CouchDB INSTALL.Unix

A high-level guide to Unix-like systems, inc. Mac OS X and Ubuntu.

Community installation guides are available on the wiki:

    http://wiki.apache.org/couchdb/Installation

If you are trying to build CouchDB from a git checkout rather than
a .tar.gz, see the `DEVELOPERS` file.

This document is the canonical source of installation
information. However, many systems have gotchas that you need to be
aware of. In addition, dependencies frequently change as distributions
update their archives. If you're running into trouble, be sure to
check out the wiki. If you have any tips to share, please also update
the wiki so that others can benefit from your experience.

## Troubleshooting

There is a troubleshooting guide:

    http://wiki.apache.org/couchdb/Troubleshooting

There is a wiki for general documentation:

    http://wiki.apache.org/couchdb/

There are collection of friendly mailing lists:

    http://couchdb.apache.org/community/lists.html

Please work through these in order if you experience any problems.

## Dependencies

You should have the following installed:

 * Erlang OTP (>= 19.x)         (http://erlang.org/)
 * ICU                          (http://icu-project.org/)
 * OpenSSL                      (http://www.openssl.org/)
 * Mozilla SpiderMonkey - either 1.8.5 or 60
   * 60 is not supported on ARM 64-bit (aarch64) at this time.
   * https://developer.mozilla.org/en/docs/Mozilla/Projects/SpiderMonkey/Releases/1.8.5
   * https://archive.mozilla.org/pub/firefox/releases/60.9.0esr/source/ (src/js)
 * GNU Make                     (http://www.gnu.org/software/make/)
 * GNU Compiler Collection      (http://gcc.gnu.org/)
 * Python (>=3.5)               (http://python.org/)

To build Fauxton, you should have the following installed:
 * Node.JS (>=10.x)             (https://nodejs.org/)
   -- obtainable from NodeSource (https://github.com/nodesource/distributions)

It is recommended that you install Erlang OTP 20.3.8.11 or above where
possible.  Note that the latest Erlang OTP versions might not be supported
properly and they may produce build errors.

You can disable Fauxton and/or the documentation builds by adding the
`--disable-fauxton` and/or `--disable-docs` flag(s) to the `configure` script.

### Debian-based Systems

You can install the dependencies by running:

    sudo apt-get --no-install-recommends -y install \
        build-essential pkg-config erlang erlang-reltool \
        libicu-dev libmozjs-60-dev python3

Your distribution may have `libmozjs-68-dev` instead of 60. Both are supported.

You can install Node.JS [NodeSource](https://github.com/nodesource/distributions#installation-instructions).

Be sure to update the version numbers to match your system's available
packages.

### RedHat-based (Fedora, Centos, RHEL) Systems

You can install the dependencies by running:

    sudo yum install autoconf autoconf-archive automake \
        erlang-asn1 erlang-erts erlang-eunit erlang-xmerl \
        libmozjs-60-dev libicu-devel libtool perl-Test-Harness \
        python3

You can install Node.JS via [NodeSource](https://github.com/nodesource/distributions#rpminstall).

### Mac OS X

To build CouchDB from source on Mac OS X, you will need to install
the Command Line Tools:

    xcode-select --install

You can then install the other dependencies by running:

    brew install autoconf autoconf-archive automake libtool \
        erlang icu4c spidermonkey pkg-config

You can install Node.JS via the
[official Macintosh installer](https://nodejs.org/en/download/).

You will need Homebrew installed to use the `brew` command.

Learn more about Homebrew at:

    http://mxcl.github.com/homebrew/

Some versions of Mac OS X ship a problematic OpenSSL library. If
you're experiencing troubles with CouchDB crashing intermittently with
a segmentation fault or a bus error, you will need to install your own
version of OpenSSL. See the wiki, mentioned above, for more information.

### FreeBSD

FreeBSD requires the use of GNU Make. Where `make` is specified in this
documentation, substitute `gmake`.

You can install this by running:

    pkg install gmake

You can install the remaining dependencies by running:

    pkg install openssl icu git bash autoconf \
        www/node npm libtool spidermonkey60 \
        erlang lang/python

## Installing

Once you have satisfied the dependencies you should run:

    ./configure

If you wish to customize the installation, pass `--help` to this
script.

If everything was successful you should see the following message:

    You have configured Apache CouchDB, time to relax.

Relax.

To build CouchDB you should run:

    make release

Try `gmake` if `make` is giving you any problems.

If everything was successful you should see the following message:

    ... done
    You can now copy the rel/couchdb directory anywhere on your system.
    Start CouchDB with ./bin/couchdb from within that directory.

Relax.

## User Registration

For OS X, in the steps below, substitute `/Users/couchdb` for `/home/couchdb`.

You should create a special `couchdb` user for CouchDB.

On many Unix-like systems you can run:

    adduser --system \
            --home /opt/couchdb \
            --no-create-home \
            --shell /bin/bash \
            --group --gecos \
            "CouchDB Administrator" couchdb

On Mac OS X you can use the Workgroup Manager to create users up to version
10.9, and dscl or sysadminctl after version 10.9. Search Apple's support
site to find the documentation appropriate for your system. As of recent
versions of OS X, this functionality is also included in Server.app,
available through the App Store only as part of OS X Server.

You must make sure that the user has a working POSIX shell.

You can test this by:

    * Trying to log in as the `couchdb` user

    * Running `pwd` and checking the present working directory

Copy the built couchdb release to the new user's home directory:

    cp -R /path/to/couchdb/rel/couchdb /opt/couchdb

Change the ownership of the CouchDB directories by running:

    chown -R couchdb:couchdb /opt/couchdb

Change the permission of the CouchDB directories by running:

    find /opt/couchdb -type d -exec chmod 0770 {} \;

Update the permissions for your ini files:

    chmod 0644 /opt/couchdb/etc/*

## First Run

You can start the CouchDB server by running:

    sudo -i -u couchdb couchdb/bin/couchdb

This uses the `sudo` command to run the `couchdb` command as the
`couchdb` user.

When CouchDB starts it should eventually display the following
message:

    Apache CouchDB has started, time to relax.

Relax.

To check that everything has worked, point your web browser to:

    http://127.0.0.1:5984/_utils/

From here you should verify your installation by pointing your web browser to:

    http://localhost:5984/_utils/#/verifyinstall

## Running as a daemon

The couchdb team recommends [runit](http://smarden.org/runit/) to
run CouchDB persistently and reliably. Configuration of runit is
straightforward; if you have questions, reach out to the CouchDB
user mailing list.

Naturally, you can configure systemd, launchd or SysV-init daemons to
launch CouchDB and keep it running using standard configuration files.
Sample scripts are in the couchdb-pkg repository:

* SysV-init (Debian-style): https://github.com/apache/couchdb-pkg/blob/master/debian/couchdb.init
* SysV-init (RHEL-style): https://github.com/apache/couchdb-pkg/blob/master/rpm/SOURCES/couchdb.init
* upstart: Use the Debian-style sysvinit script instead.
* systemd: https://github.com/apache/couchdb-pkg/blob/master/debian/couchdb.service

Consult your system documentation for more information.
