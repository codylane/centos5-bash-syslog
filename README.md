centos5-bash-syslog
===================

All the components you need to get syslog enabled on bash bash-3.2-32.1. 


Steps Used To Create Bash Syslog
================================

Thanks to https://github.com/migrantgeek/bash-syslog.git for the helpful information. 
* Download the SRPM from the vault to your CentOS 5 machine you will use to rebuild this RPM
```
wget http://vault.centos.org/5.10/os/SRPMS/bash-3.2-32.el5_9.1.src.rpm
```
* Cloned the git repository to your CentOS 5 machine
```
git clone https://github.com/migrantgeek/bash-syslog.git
```
* Using the bash.spec in the cloned directory, I did diff on the SRPM version /usr/src/redhat/SPECS/bash.spec because I wanted to make sure there was no securiy concerns and to make sure the recent patches were applied.  
* Using the diff results found in the github repository vs the SRPM spec file /usr/src/redhat/SPECS/bash.spec.  I manually updated/added information I cared about to /usr/src/redhat/SPECS/bash.spec.  Here is the resulting diff.
```
[root@rpmbuild5 SPECS]# diff -Naur /usr/src/redhat/SPECS/bash.spec bash.spec 
--- /usr/src/redhat/SPECS/bash.spec	2013-06-27 02:43:12.000000000 -0600
+++ bash.spec	2014-02-18 11:01:49.000000000 -0700
@@ -104,6 +104,10 @@
 # 978840, fix deadlock in trap, backported from devel branch
 Patch155: bash-3.2-bz964753.patch
 
+# A patch for syslog for bash 3.2 found on github
+Patch156: bash-3.2-syslog.patch
+
+
 Requires: mktemp
 BuildRoot: %{_tmppath}/%{name}-%{version}-%{release}-root-%(%{__id_u} -n)
 
@@ -191,6 +195,8 @@
 
 %patch155 -p1 -b .bz964753
 
+%patch156 -p1 -b .syslog.patch
+
 echo %{version} > _distribution
 echo %{release} > _patchlevel
 
@@ -331,6 +337,10 @@
 %doc doc/*.ps doc/*.0 doc/*.html doc/article.txt
 
 %changelog
+* Tue Feb 18 2014 Seth Miller <seth@migrantgeek.com> - 3.2-24
+- Added bash-3.2-syslog-patch
+  Patch will log all commands to LOCAL5 with the username and PID
+
 * Thu Jun 27 2013 Roman Rakus <rrakus@redhat.com> - 3.2-32.1
 - Fixed a bug that caused trap handlers to be executed recursively,
   corrupting internal data structures.
```


Rebuild the RPM
===============
NOTE: Ensure you have a CentOS/RHEL 5.x machine setup with the rpm-build packages.  Also some familiarity for how rpmbuild works would be helpful if there are issues.  

NOTE: The recommended way for building RPMS is using a non-root user, however, I normally build RPMS as root in a test VM that is disposable.

Download the SRPM version on your CentOS 5.x machine
```
wget http://vault.centos.org/5.10/os/SRPMS/bash-3.2-32.el5_9.1.src.rpm
rpm -ivh bash-3.2-32.el5_9.1.src.rpm
```

* Copy the syslog patch for the C library  
```
sudo su -
cd /root
git clone https://github.com/codylane/centos5-bash-syslog
cp /root/centos5-bash-syslog/bash-3.2-syslog.patch /usr/src/redhat/SOURCES/
```

* Copy the patch file from this repository to the SPECS directory
```
cp /root/centos5-bash-syslog/bash3.2-32.1.spec.patch /usr/src/redhat/SPECS
yum install patch
cd /usr/src/redhat/SPECS
patch < bash3.2-32.1.spec.patch
```

* rebuild the RPM
```
rpmbuild -ba bash.spec
```
