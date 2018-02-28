OSSO build of Unixodbc packages for Debian (2.3.1-4.1)
======================================================

Download the original tar file, from http://www.unixodbc.org/download.html
as unixodbc_VERSION.orig.tar.gz and extract::

    wget http://www.unixodbc.org/unixODBC-2.3.4.tar.gz -O unixodbc_2.3.4.orig.tar.gz

    md5sum -c << EOF
    bd25d261ca1808c947cb687e2034be81  unixodbc_2.3.4.orig.tar.gz
    EOF

    tar zxf unixodbc_2.3.4.orig.tar.gz

Git clone this repository into the debian directory, and choose the right branch::

    cd unixODBC-2.3.4/
    git clone https://github.com/ossobv/unixodbc-deb.git debian
    git branch -a

    git checkout CHOSEN_BRANCH

Optionally update the changelog.

Build::

    DEB_BUILD_OPTIONS=parallel=6 dpkg-buildpackage -us -uc -sa


Creating a libmyodbc for Debian/Squeeze
---------------------------------------

Take the libmyodbc files from Wheezy::

    wget http://http.debian.net/debian/pool/main/m/myodbc/myodbc_5.1.10-2+deb7u1.dsc
    wget http://http.debian.net/debian/pool/main/m/myodbc/myodbc_5.1.10.orig.tar.gz
    wget http://http.debian.net/debian/pool/main/m/myodbc/myodbc_5.1.10-2+deb7u1.diff.gz

Untar and patch::

    tar zxf myodbc_5.1.10.orig.tar.gz

    cd mysql-connector-odbc-5.1.10-src/
    zcat ../myodbc_5.1.10-2+deb7u1.diff.gz | patch -p1

Apply this patch by cd'ing into ``debian/`` and pasting this in ``patch -lp1``::

    diff --git a/changelog b/changelog
    index f6ae941..5ce9a62 100644
    --- a/changelog
    +++ b/changelog
    @@ -1,3 +1,9 @@
    +myodbc (5.1.10-2+deb7u1osso0~squeeze) squeeze; urgency=low
    +
    +  * Take wheezy package and build for squeeze.
    +
    + -- Walter Doekes <wjdoekes@osso.nl>  Wed, 14 Sep 2016 22:30:39 +0200
    +
     myodbc (5.1.10-2+deb7u1) wheezy; urgency=low

       * Non-maintainer upload.
    diff --git a/compat b/compat
    index ec63514..45a4fb7 100644
    --- a/compat
    +++ b/compat
    @@ -1 +1 @@
    -9
    +8
    diff --git a/control b/control
    index ea35212..91aa5e9 100644
    --- a/control
    +++ b/control
    @@ -3,7 +3,7 @@ Section: libs
     Priority: optional
     Maintainer: Steve Langasek <vorlon@debian.org>
     Standards-Version: 3.9.2
    -Build-Depends: debhelper (>= 8.9.4), libmysqlclient-dev (>= 5.5.17), zlib1g-dev (>= 1:1.1.3), unixodbc-dev (>= 2.2.11), libltdl3-dev, autotools-dev, dh-autoreconf
    +Build-Depends: debhelper (>= 8), libmysqlclient-dev (>= 5.1.73), zlib1g-dev (>= 1:1.1.3), unixodbc-dev (>= 2.2.11), libltdl3-dev, autotools-dev, dh-autoreconf
     Homepage: http://dev.mysql.com/downloads/connector/odbc/
     Vcs-Bzr: nosmart+http://bzr.debian.org/bzr/users/vorlon/myodbc/trunk/
     Vcs-Browser: http://anonscm.debian.org/loggerhead/users/vorlon/myodbc/trunk/
    diff --git a/libmyodbc.install b/libmyodbc.install
    index f761d2a..5e6c80c 100644
    --- a/libmyodbc.install
    +++ b/libmyodbc.install
    @@ -1,2 +1,2 @@
    -usr/lib/*/odbc/libmyodbc.so
    +usr/lib/odbc/libmyodbc.so
     debian/odbcinst.ini            usr/share/libmyodbc

Change directory back to ``mysql-connector-odbc-5.1.10-src/`` again and compile::

    # make sure you install some kind of unixodbc-dev>=2.2.11 like
    # unixodbc_2.3.1-4.1osso0~squeeze from
    # https://github.com/ossobv/unixodbc-deb/tree/squeeze

    DEB_BUILD_OPTIONS=parallel=6 dpkg-buildpackage -us -uc -sa

You should now have these files which should install properly on Debian/Squeeze::

    libmyodbc_5.1.10-2+deb7u1osso0~squeeze_amd64.deb
    myodbc_5.1.10-2+deb7u1osso0~squeeze.diff.gz
    myodbc_5.1.10-2+deb7u1osso0~squeeze.dsc
    myodbc_5.1.10-2+deb7u1osso0~squeeze_amd64.changes
    myodbc_5.1.10.orig.tar.gz

