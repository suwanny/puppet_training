# Resource Default


### [root@spark ~/puppetcode/modules:master±]# tree

```
.
├── apache
│   ├── files
│   │   ├── conf
│   │   │   └── httpd.conf
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

12 directories, 10 files
```

### [root@spark ~/puppetcode/modules:master±]# cat apache/manifests/init.pp 

```
class apache {
  File {
    owner => 'apache',
    group => 'apache',
    mode  => '0644',
  }
  package { 'httpd':
    ensure        => '2.2.15-30.el6.centos',
    allow_virtual => false,
  }
  file { '/var/www':
    ensure => directory,
  }
  file { '/var/www/html':
    ensure => directory,
  }
  file { '/var/www/conf':
    ensure => directory,
  }
  file { '/var/www/html/index.html':
    ensure => file,
    source => 'puppet:///modules/apache/html/index.html',
  }
  file { '/var/www/conf/httpd.conf':
    ensure => file,
    owner  => 'root',
    source => 'puppet:///modules/apache/conf/httpd.conf',
  }
  service { 'httpd':
    ensure => 'running',
    enable => true,
  }
}
```

### [root@spark ~/puppetcode/modules:master±]# puppet apply apache/tests/init.pp

```
Notice: Compiled catalog for spark.puppetlabs.vm in environment production in 0.37 seconds
Notice: Finished catalog run in 0.28 seconds
```

> [root@spark ~/puppetcode/modules:master±]# vi apache/files/html/index.html 

### [root@spark ~/puppetcode/modules:master±]# puppet apply apache/tests/init.pp

```
Notice: Compiled catalog for spark.puppetlabs.vm in environment production in 0.59 seconds
Notice: /Stage[main]/Apache/File[/var/www/html/index.html]/content: content changed '{md5}8d2745f88174a818a82165d64ecce4cf' to '{md5}540f94236117db7dbe04af21d45c057b'
Notice: Finished catalog run in 0.50 seconds
```

