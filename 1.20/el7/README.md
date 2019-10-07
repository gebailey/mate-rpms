# MATE 1.20 rebuild for RHEL/CentOS 7

This repo contains scripts to rebuild MATE 1.20 RPMs for a RHEL/CentOS 7
environment.

The EPEL repository for RHEL/CentOS 7 distributions currently has MATE 1.16
RPMs.  [Bugzilla 1436260](https://bugzilla.redhat.com/show_bug.cgi?id=1436260)
requests a newer MATE version be made available, but so far only MATE 1.18 is
available in a [COPR
repository](https://copr.fedorainfracloud.org/coprs/raveit65/Mate-GTK3/).  The
current maintainer of the MATE packages for Fedora has no interest in
maintaining MATE for epel7 or epel8.

Almost all of the MATE 1.20 packages can be rebuilt on CentOS 7 with minimal
patches.  For each MATE component, the approach taken is to:

* Use the `fedpkg` tool to clone the Fedora git repo from the master (rawhide) branch
* Perform a `git checkout` to the commit just before MATE 1.22 was committed
* Apply any patches required for epel compatibility (see below)
* Use `fedpkg --release epel7 srpm` to construct a source RPM
* Use `mock --rebuild ...` to rebuild the source RPM

These scripts are assumed to be running on a host running CentOS 7, with the
EPEL yum repository enabled.  Running these scripts will require approximately
3.5GB of disk space.

# Using pre-built MATE 1.20 RPMS

To use the pre-built MATE 1.20 RPMs that were built using these scripts, create
a yum repo configuration (the following assumes you're running as **root**):

```bash
cat > /etc/yum.repos.d/mate-1.20-el7.repo <<EOF
[mate-1.20-el7]
name=MATE 1.20 RPMs for EL7
baseurl=http://repos.lxpro.com/mate/1.20/el7
enabled=0
gpgcheck=1
gpgkey=https://lxpro.com/RPM-GPG-KEY-repos.lxpro.com
EOF
```

To upgrade:

```bash
yum --enablerepo=mate-1.20-el7 upgrade
```

The public signing key (repos.lxpro.com) used for this repo is:

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v2.0.22 (GNU/Linux)

mQINBF2Z1WIBEADj4CXPUaeN2PgZ0Z/Qk3v6JzasFiGKX9FjyLri6cLs40zxiZeY
wMMDNY4j5BX4Bq2T3DTNZ2o914PDvORUmKy9rXPOO2rZ4zFgnQ8ubVNTNVfXvhah
Qz1MOXDecLHQYPn4+gqtplQ3IybI0Jq2q4lMRYpeDl/eXjV3/axCNLCXqkO8hZSB
F+dOaHO7OO1I/t/7ys6YQ4toCdLvHydi7NzbRMMc+Q+JatpDzbBoFaRYGxK+ARYk
QTpmXFDFmZRmo6qQXXzjFdvsQ6cfPfFk6A7whgAZtnNbHHAroQqq4oq2Q/Klg7WK
hoz5TBul3DcFmmUAOMSLC669dCvm9amHxgV2puvCDV0VCJjQ6QiQn/XLO6hD9A4Q
RSxVpjiyAyT/KCDTCG70FSHtY8sIX/244gHX3r7b63UOfhpZyIOzoWCd2NM+Om6p
c7F//h3fCsRk3tITMfUvd9pE3kYxYVFqQjq7LRMVBxX4/nnY/+NdjcX86KS7e96r
YHICBtDbINUXHZx5CLfylx9j9sUcv8989E0ALYaSUlHOPHAv4yCqmOzk4FPEThgr
yXI9JY4JOIjwsEMoZI9kJwQQc4R3SekZEqwsn3exmq/xYuwePqTi5jmK4blg/Bkv
QyLKm0fVIX4pMyj7zru/MgfYt9kBqp75A4VBUHK7GQEtfqBIS0I4oOPaLwARAQAB
tCFyZXBvcy5seHByby5jb20gPHJlcG9zQGx4cHJvLmNvbT6JAjkEEwECACMFAl2Z
1WICGwMHCwkIBwMCAQYVCAIJCgsEFgIDAQIeAQIXgAAKCRBL7/Pd+LSCARcpEADP
Hv0IxO56d+83uvjJx3KtCb2oawLWw17Tik/5idUZjgjZLaDakJMjFv7bkyY2dxZA
7BBdQ6xg/DLYwPjwVBZNxKlyiLOYyy8PewWO9TVaxGmFGtdNyciJbrWkLh8Ap9Rm
oLQFAvO8JTBFV0Uhtzo5Zr1/xeuDRPjYmF4UmEO+yWw2VkT4yWn02K4HpQADfdUC
kdDKhxmuOrFvaqnySbd94LiTteFLs4zZvZ7Tp4L8qWzVXuGvuVijLB9Ld+G+F4nY
ihI8RzR3jX71iSXKLSOcI7foTGrTBpD7zrk8SnddPBj7AQj7pfmxudNEJdtinClS
E8DmWItseULWYoSmjHtmRHoIyTeUn77SJv+4cbGNX6Qr4fc5Oju5bJasbZAK51wk
dqer6rBMYmoZ0FybVsrtzd1VeAh8Mo/BDRqKJs77AQ0+cNwjcu9s3GFuNzyBYa6X
QUB+Zmupf6bVooothe3Tw/3tXDCv5ikq7RsWUW/KdOKVHKvZPgm9jPn4/mY36k6P
gpMYvyQsZKDMHjg3uqYTnD43LSlT6FeDfmO2Yid2RtEzJfLeAGsVQla1N/VZrkl+
7e8nZQSeLiGcLZncB1oIy6cEwxMJC/Hp5Yp0cCg/aFWvy0p/lZjpA2oMjMmMEkYU
4RIT7a3Vu26DH1Gdy/IfwrhxzRk0wbWxKQ6mJUKoRQ==
=8LoU
-----END PGP PUBLIC KEY BLOCK-----
```

The rest of these instructions cover building your own set of MATE 1.20 RPMs.

# Mock environment setup

```
./setup_mock
```

# Build dependencies

Certain packages from the EPEL repository need to be available to build MATE
RPMs, but we can't just enable EPEL in the mock build root because there's old
MATE RPMs we don't want to pull in.  Instead, just populate a separate yum
repository with these required RPMs and make it available to the mock chroot.

| MATE component | EPEL build requirements
| --- | ---
| caja | pangox-compat, pangox-compat-devel
| atril | djvulibre-devel, djvulibre-libs
| caja-extensions | gajim, python-nbxmpp, python2-crypto, python2-gnupg, libtomcrypt, libtommath
| mate-applets | wireless-tools, wireless-tools-devel
| mate-panel (runtime) | fortune-mod
| mate-sensors-applet | libXNVCtrl, libXNVCtrl-devel

```
./setup_deps
```

# Build source RPMs

Source RPMs are constructed by performing the following operations on the
Fedora git repository for each MATE package:

* Use `fedpkg clone -a <package-name>`
* Use `git log` to find last commit before MATE 1.22 changes were made
* Use `git checkout <git-commit-hash>`
* Use `fedpkg --release epel7 srpm`

The `make_source_rpms` script uses a config file that specifies the git commit
hash.

Some of the source files need to be patched because:

* Some conditional macros don't include `%{rhel}` (caja)
* `Recommends:` isn't support in EL7 (mate-panel)
* EL7 [needs calls](https://fedoraproject.org/wiki/EPEL:Packaging#Shared_Libraries) to `/sbin/ldconfig` in RPM scriptlets
* EL7 [needs calls](https://fedoraproject.org/wiki/EPEL:Packaging#GSettings_Schema) to `/usr/bin/glib-compile-schemas` in RPM scriptlets
* EL7 [needs calls](https://fedoraproject.org/wiki/EPEL:Packaging#mimeinfo) to `/usr/bin/update-mime-database` in RPM scriptlets
* EL7 [needs calls](https://fedoraproject.org/wiki/EPEL:Packaging#desktop-database) to `/usr/bin/update-desktop-database` in RPM scriptlets
* EL7 [needs calls](https://fedoraproject.org/wiki/EPEL:Packaging#Icon_Cache) to `/usr/bin/gtk-update-icon-cache` in RPM scriptlets

```
./make_source_rpms
```

# Build binary RPMs

`mate-applets` has a dependency on `mate-settings-daemon`, which has a runtime
dependency on `mate-control-center-filesystem`, which has a dependency on
`mate-settings-daemon-devel`, which has a dependency on `mate-settings-daemon`.

To work around this, we manually install `mate-settings-daemon` in the build
root and ignore dependencies.  The second run of `./make_binary_rpms` will then
succeed.

```
./make_binary_rpms

MATE_SETTINGS_DAEMON=$(ls /var/www/html/mate1.20/mate-settings-daemon-1.20*.x86_64.rpm)

sudo mock -r mate1.20-7-x86_64 --copyin ${MATE_SETTINGS_DAEMON} mate-settings-daemon.rpm
sudo mock -r mate1.20-7-x86_64 --shell 'rpm -ivh mate-settings-daemon.rpm --nodeps'

./make_binary_rpms
```

# Yum Repositories

Separate build artifacts into binary, source, and debuginfo yum repositories:

```
./make_repos
```

(Optionally) sign the RPMs and make them available somewhere, such as S3 or a
local webserver:

```
cd repos/mate1.20
aws s3 sync . s3://repos.lxpro.com/mate/1.20/el7
```

