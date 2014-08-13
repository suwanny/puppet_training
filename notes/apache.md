# Apache


## tree

```
[root@spark /etc/puppetlabs/puppet/modules:master±]# tree
.
├── apache
│   ├── files
│   │   └── html
│   │       └── index.html
│   ├── manifests
│   │   └── init.pp
│   └── tests
│       └── init.pp
├── hosts
│   ├── manifests
│   │   └── init.pp
│   └── tests
│       └── init.pp
└── users
    ├── manifests
    │   ├── admins.pp
    │   └── init.pp
    └── tests
        ├── admins.pp
        └── init.pp

11 directories, 9 files
```

### cat apache/manifests/init.pp

```
class apache {
  package { 'httpd':
    ensure        => '2.2.15-30.el6.centos',
    allow_virtual => false,
  }

  file { '/var/www':
    ensure => directory,
    mode   => '0755',
  }

  file { '/var/www/html':
    ensure => directory,
    mode   => '0755',
  }

  file { '/var/www/html/index.html':
    ensure => file,
    mode   => '0755',
    source => 'puppet:///modules/apache/html/index.html',
  }

  service { 'httpd':
    ensure => 'running',
    enable => true,
  }
}
```

### Others

```
[root@spark /etc/puppetlabs/puppet/modules:master±]# cat apache/tests/init.pp
include apache

[root@spark /etc/puppetlabs/puppet/modules:master±]# cat apache/files/html/index.html
<h1>Hello, Puppet Class</h1>
```

### puppet apply --noop apache/tests/init.pp

```
Notice: /Stage[main]/Apache/Package[httpd]/ensure: current_value absent, should be present (noop)
Notice: /Stage[main]/Apache/File[/var/www]/ensure: current_value absent, should be directory (noop)
Notice: /Stage[main]/Apache/File[/var/www/html]/ensure: current_value absent, should be directory (noop)
Notice: /Stage[main]/Apache/File[/var/www/html/index.html]/ensure: current_value absent, should be file (noop)
Notice: Class[Apache]: Would have triggered 'refresh' from 4 events
Notice: Stage[main]: Would have triggered 'refresh' from 1 events
Notice: Finished catalog run in 0.35 seconds
```

### puppet apply apache/tests/init.pp

```
Notice: /Stage[main]/Apache/Package[httpd]/ensure: created
Notice: /Stage[main]/Apache/File[/var/www/html/index.html]/ensure: defined content as '{md5}8d2745f88174a818a82165d64ecce4cf'
Notice: Finished catalog run in 15.60 seconds
```

### yum info httpd

```
Loaded plugins: fastestmirror, priorities, security
Loading mirror speeds from cached hostfile
 * base: mirrors.kernel.org
 * epel: mirror.pnl.gov
 * extras: mirrors.cat.pdx.edu
 * updates: mirrors.sonic.net
182 packages excluded due to repository priority protections
Installed Packages
Name        : httpd
Arch        : i686
Version     : 2.2.15
Release     : 30.el6.centos
Size        : 2.8 M
Repo        : installed
From repo   : base_local
Summary     : Apache HTTP Server
URL         : http://httpd.apache.org/
License     : ASL 2.0
Description : The Apache HTTP Server is a powerful, efficient, and extensible
            : web server.
```

### rpm -q httpd

```
httpd-2.2.15-30.el6.centos.i686
```

