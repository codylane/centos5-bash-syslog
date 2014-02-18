centos5-bash-syslog
===================

All the components you need to get syslog enabled on bash bash-3.2-32.1.  This includes all the source files, patch files... etc.


Thanks to https://github.com/migrantgeek/bash-syslog.git for the helpful information. 

Rebuild the RPM
===============
NOTE: Ensure you have a 5.x machines setup with the rpm-build packages.  

Next download tarball 
```
tar zxvf centos5-bash-3.2-32.1-syslog.tar.gz -C /
cd /usr/src/redhat/SPECS
rpmbuild -ba bash.spec
```
