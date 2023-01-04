---
layout: page
title: KB005001 Redhat Enterprise Linux RHCSA
---

Package management

```bash
root@rhel:~# yum module list postgresql
Updating Subscription Management repositories.
Last metadata expiration check: 0:02:15 ago on Wed 04 Jan 2023 06:55:54 AM UTC.
Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
Name                   Stream             Profiles                      Summary                                       
postgresql             9.6                client, server [d]            PostgreSQL server and client module           
postgresql             10 [d]             client, server [d]            PostgreSQL server and client module           
postgresql             12                 client, server [d]            PostgreSQL server and client module           
postgresql             13                 client, server [d]            PostgreSQL server and client module           

Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
root@rhel:~# 
```

Note the `[d]` next to version 10.  This means if no options were selected then version 10 would be installed - the default.

Example usage:

```bash
yum -y module install postgresql:9.6
yum module list postgresql
yum -y module remove postgresql:9.6
yum -y module disable postgresql
```
