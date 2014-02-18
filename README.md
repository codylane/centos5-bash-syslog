centos5-bash-syslog
===================

All the components you need to get syslog enabled on bash bash-3.2-32.1.  This includes all the source files, patch files... etc.

This binary was created from the modified version of the CentOS 5.10 SRPM found here: http://vault.centos.org/5.10/os/SRPMS/bash-3.2-32.el5_9.1.src.rpm

Steps I used to create this tarball
===================================

Thanks to https://github.com/migrantgeek/bash-syslog.git for the helpful information. 

* I reviewed the patch file from the github repository below, added it to /usr/src/redhat/SOURCES
* diffed the spec found in the github repoistory and the one from the SRPM /usr/src/redhat/SPECS/bash.spec
* Ensured there were no security concerns
* Using the diff results found in the github repository vs the spec file /usr/src/redhat/SPECS/bash.spec.  I manually updated/added information I cared about to /usr/src/redhat/SPECS/bash.spec.  Here is the resulting diff.
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

Next download tarball 
```
tar zxvf centos5-bash-3.2-32.1-syslog.tar.gz -C /
cd /usr/src/redhat/SPECS
rpmbuild -ba bash.spec
```
