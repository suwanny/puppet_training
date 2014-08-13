# Service

## puppet describe service

```
service
=======
Manage running services.  Service support unfortunately varies
widely by platform --- some platforms have very little if any concept of a
running service, and some have a very codified and powerful concept.
Puppet's service support is usually capable of doing the right thing, but
the more information you can provide, the better behaviour you will get.
Puppet 2.7 and newer expect init scripts to have a working status command.
If this isn't the case for any of your services' init scripts, you will
need to set `hasstatus` to false and possibly specify a custom status
command in the `status` attribute. As a last resort, Puppet will attempt to
search the process table by calling whatever command is listed in the `ps`
fact. The default search pattern is the name of the service, but you can
specify it with the `pattern` attribute.
**Refresh:** `service` resources can respond to refresh events (via
`notify`, `subscribe`, or the `~>` arrow). If a `service` receives an
event from another resource, Puppet will restart the service it manages.
The actual command used to restart the service depends on the platform and
can be configured:
* If you set `hasrestart` to true, Puppet will use the init script's restart
command.
* You can provide an explicit command for restarting with the `restart`
attribute.
* If you do neither, the service's stop and start commands will be used.


Parameters
----------

- **binary**
    The path to the daemon.  This is only used for
    systems that do not support init scripts.  This binary will be
    used to start the service if no `start` parameter is
provided.

- **control**
    The control variable used to manage services (originally for HP-UX).
    Defaults to the upcased service name plus `START` replacing dots with
    underscores, for those providers that support the `controllable`
    feature.

- **enable**
    Whether a service should be enabled to start at boot.
    This property behaves quite differently depending on the platform;
    wherever possible, it relies on local tools to enable or disable
    a given service.
    Valid values are `true`, `false`, `manual`. 
    Requires features enableable.

- **ensure**
    Whether a service should be running.
    Valid values are `stopped` (also called `false`), `running` (also called
    `true`). 

- **flags**
    Specify a string of flags to pass to the startup script.
    Requires features flaggable.

- **hasrestart**
    Specify that an init script has a `restart` command.  If this is
    false and you do not specify a command in the `restart` attribute,
    the init script's `stop` and `start` commands will be used.
    Defaults to false.
Valid values are `true`, `false`. 

- **hasstatus**
    Declare whether the service's init script has a functional status
    command; defaults to `true`. This attribute's default value changed in
    Puppet 2.7.0.
    The init script's status command must return 0 if the service is
    running and a nonzero value otherwise. Ideally, these exit codes
    should conform to [the LSB's specification][lsb-exit-codes] for init
    script status actions, but Puppet only considers the difference
    between 0 and nonzero to be relevant.
    If a service's init script does not support any kind of status command,
    you should set `hasstatus` to false and either provide a specific
    command using the `status` attribute or expect that Puppet will look for
    the service name in the process table. Be aware that 'virtual' init
    scripts (like 'network' under Red Hat systems) will respond poorly to
    refresh events from other resources if you override the default behavior
    without providing a status command.
Valid values are `true`, `false`. 

- **manifest**
    Specify a command to config a service, or a path to a manifest to do so.

- **name**
    The name of the service to run.
    This name is used to find the service; on platforms where services
    have short system names and long display names, this should be the
    short name. (To take an example from Windows, you would use "wuauserv"
    rather than "Automatic Updates.")

- **path**
    The search path for finding init scripts.  Multiple values should
    be separated by colons or provided as an array.

- **pattern**
    The pattern to search for in the process table.
    This is used for stopping services on platforms that do not
    support init scripts, and is also used for determining service
    status on those service whose init scripts do not include a status
    command.
    Defaults to the name of the service. The pattern can be a simple string
    or any legal Ruby pattern, including regular expressions (which should
    be quoted without enclosing slashes).

- **restart**
    Specify a *restart* command manually.  If left
    unspecified, the service will be stopped and then started.

- **start**
    Specify a *start* command manually.  Most service subsystems
    support a `start` command, so this will not need to be
specified.

- **status**
    Specify a *status* command manually.  This command must
    return 0 if the service is running and a nonzero value otherwise.
    Ideally, these exit codes should conform to [the LSB's
    specification][lsb-exit-codes] for init script status actions, but
    Puppet only considers the difference between 0 and nonzero to be
    relevant.
    If left unspecified, the status of the service will be determined
    automatically, usually by looking for the service in the process
    table.
    [lsb-exit-codes]:
    http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-
    generic/iniscrptact.html

- **stop**
    Specify a *stop* command manually.

Providers
---------
    base, bsd, daemontools, debian, freebsd, gentoo, init, launchd, openbsd,
    openrc, openwrt, redhat, runit, service, smf, src, systemd, upstart,
    windows
```

