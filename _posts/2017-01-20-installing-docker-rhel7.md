---
layout: post
title: Installing Docker on RHEL7
---
on RHEL7 docker is available in the extras channel, we can see that it is disabled

{% highlight base %}
[sean@localhost ~]$ subscription-manager repos --list | grep -i extras
Password: 
Repo ID:   rhel-7-server-extras-rpms
Repo Name: Red Hat Enterprise Linux 7 Server - Extras (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel/server/7/7Server/$basearch/extras/os
Repo ID:   rhel-7-server-extras-source-rpms
Repo Name: Red Hat Enterprise Linux 7 Server - Extras (Source RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel/server/7/7Server/$basearch/extras/source/SRPMS
Repo ID:   rhel-7-server-extras-debug-rpms
Repo Name: Red Hat Enterprise Linux 7 Server - Extras (Debug RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel/server/7/7Server/$basearch/extras/debug
{% endhighlight  %}

Lets enable it

{% highlight bash %}
[sean@localhost ~]$ sudo subscription-manager repos --enable rhel-7-server-extras-rpms
[sudo] password for sean: 
Repository 'rhel-7-server-extras-rpms' is enabled for this system.
{% endhighlight  %}

Now we can see the packages

{% highlight bash %}
[sean@localhost ~]$ sudo yum list "*docker*"
Loaded plugins: product-id, search-disabled-repos, subscription-manager
rhel-7-server-eus-rpms                                                                                                                                                               | 3.5 kB  00:00:00     
rhel-7-server-extras-rpms                                                                                                                                                            | 3.4 kB  00:00:00     
rhel-7-server-optional-rpms                                                                                                                                                          | 3.5 kB  00:00:00     
rhel-7-server-rpms                                                                                                                                                                   | 3.5 kB  00:00:00     
rhel-7-server-rt-beta-rpms                                                                                                                                                           | 4.0 kB  00:00:00     
rhel-7-server-rt-rpms                                                                                                                                                                | 4.0 kB  00:00:00     
rhel-ha-for-rhel-7-server-eus-rpms                                                                                                                                                   | 3.4 kB  00:00:00     
rhel-ha-for-rhel-7-server-rpms                                                                                                                                                       | 3.4 kB  00:00:00     
rhel-rs-for-rhel-7-server-eus-rpms                                                                                                                                                   | 3.4 kB  00:00:00     
rhel-rs-for-rhel-7-server-rpms                                                                                                                                                       | 3.4 kB  00:00:00     
(1/3): rhel-7-server-extras-rpms/x86_64/group                                                                                                                                        |  104 B  00:00:00     
(2/3): rhel-7-server-extras-rpms/x86_64/updateinfo                                                                                                                                   | 120 kB  00:00:00     
(3/3): rhel-7-server-extras-rpms/x86_64/primary_db                                                                                                                                   | 162 kB  00:00:00     
Available Packages
cockpit-docker.x86_64                                                                              122-3.el7                                                                       rhel-7-server-extras-rpms
docker.x86_64                                                                                      2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
docker-common.x86_64                                                                               2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
docker-distribution.x86_64                                                                         2.5.1-1.el7                                                                     rhel-7-server-extras-rpms
docker-forward-journald.x86_64                                                                     1.10.3-44.el7                                                                   rhel-7-server-extras-rpms
docker-latest.x86_64                                                                               1.12.3-10.el7                                                                   rhel-7-server-extras-rpms
docker-latest-logrotate.x86_64                                                                     1.12.3-10.el7                                                                   rhel-7-server-extras-rpms
docker-latest-v1.10-migrator.x86_64                                                                1.12.3-10.el7                                                                   rhel-7-server-extras-rpms
docker-logrotate.x86_64                                                                            2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
docker-lvm-plugin.x86_64                                                                           2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
docker-novolume-plugin.x86_64                                                                      2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
docker-python.x86_64                                                                               1.4.0-115.el7                                                                   rhel-7-server-extras-rpms
docker-registry.noarch                                                                             0.6.8-8.el7                                                                     rhel-7-server-extras-rpms
docker-registry.x86_64                                                                             0.9.1-7.el7                                                                     rhel-7-server-extras-rpms
docker-rhel-push-plugin.x86_64                                                                     2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
docker-selinux.x86_64                                                                              1.10.3-57.el7                                                                   rhel-7-server-extras-rpms
docker-v1.10-migrator.x86_64                                                                       2:1.10.3-59.el7                                                                 rhel-7-server-extras-rpms
{% endhighlight  %}

Now we can install docker

{% highlight bash %}
[sean@localhost ~]$ sudo yum -y install docker
{% endhighlight %}