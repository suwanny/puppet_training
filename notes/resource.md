# Resource

## Review yesterday

```
[root@spark ~/puppetcode:master±]# cd /etc/puppetlabs/puppet/modules/
[root@spark /etc/puppetlabs/puppet/modules:master±]# tree
.
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
```

> Module contains classes


### puppet describe file

```
file
====
Manages files, including their content, ownership, and permissions.
The `file` type can manage normal files, directories, and symlinks; the
type should be specified in the `ensure` attribute. Note that symlinks
cannot
be managed on Windows systems.
File contents can be managed directly with the `content` attribute, or
downloaded from a remote source using the `source` attribute; the latter
can also be used to recursively serve directories (when the `recurse`
attribute is set to `true` or `local`). On Windows, note that file
contents are managed in binary mode; Puppet never automatically translates
line endings.
**Autorequires:** If Puppet is managing the user or group that owns a
file, the file resource will autorequire them. If Puppet is managing any
parent directories of a file, the file resource will autorequire them.


Parameters
----------

- **backup**
    Whether (and how) file content should be backed up before being
    replaced.
    This attribute works best as a resource default in the site manifest
    (`File { backup => main }`), so it can affect all file resources.
    * If set to `false`, file content won't be backed up.
    * If set to a string beginning with `.` (e.g., `.puppet-bak`), Puppet
    will
      use copy the file in the same directory with that value as the
    extension
      of the backup. (A value of `true` is a synonym for `.puppet-bak`.)
    * If set to any other string, Puppet will try to back up to a filebucket
      with that title. See the `filebucket` resource type for more details.
      (This is the preferred method for backup, since it can be centralized
      and queried.)
    Default value: `puppet`, which backs up to a filebucket of the same
    name.
    (Puppet automatically creates a **local** filebucket named `puppet` if
    one
    doesn't already exist.)
    Backing up to a local filebucket isn't particularly useful. If you want
    to make organized use of backups, you will generally want to use the
    puppet master server's filebucket service. This requires declaring a
    filebucket resource and a resource default for the `backup` attribute
    in site.pp:
        # /etc/puppet/manifests/site.pp
        filebucket { 'main':
          path   => false,                # This is required for remote
    filebuckets.
          server => 'puppet.example.com', # Optional; defaults to the
    configured puppet master.
        }
        File { backup => main, }
    If you are using multiple puppet master servers, you will want to
    centralize the contents of the filebucket. Either configure your load
    balancer to direct all filebucket traffic to a single master, or use
    something like an out-of-band rsync task to synchronize the content on
    all
masters.

- **checksum**
    The checksum type to use when determining whether to replace a file's
    contents.
    The default checksum type is md5.
    Valid values are `md5`, `md5lite`, `sha256`, `sha256lite`, `mtime`,
    `ctime`, `none`. 

- **content**
    The desired contents of a file, as a string. This attribute is mutually
    exclusive with `source` and `target`.
    Newlines and tabs can be specified in double-quoted strings using
    standard escaped syntax --- \n for a newline, and \t for a tab.
    With very small files, you can construct content strings directly in
    the manifest...
        define resolve(nameserver1, nameserver2, domain, search) {
            $str = "search $search
                domain $domain
                nameserver $nameserver1
                nameserver $nameserver2
                "
            file { "/etc/resolv.conf":
              content => "$str",
            }
        }
    ...but for larger files, this attribute is more useful when combined
    with the
    function.
    (http://docs.puppetlabs.com/references/latest/function.html#template)
    function.

- **ctime**
    A read-only state to check the file ctime. On most modern \*nix-like
    systems, this is the time of the most recent change to the owner, group,
    permissions, or content of the file.

- **ensure**
    Whether the file should exist, and if so what kind of file it should be.
    Possible values are `present`, `absent`, `file`, `directory`, and
    `link`.
    * `present` will accept any form of file existence, and will create a
      normal file if the file is missing. (The file will have no content
      unless the `content` or `source` attribute is used.)
    * `absent` will make sure the file doesn't exist, deleting it
      if necessary.
    * `file` will make sure it's a normal file, and enables use of the
      `content` or `source` attribute.
    * `directory` will make sure it's a directory, and enables use of the
      `source`, `recurse`, `recurselimit`, `ignore`, and `purge` attributes.
    * `link` will make sure the file is a symlink, and **requires** that you
      also set the `target` attribute. Symlinks are supported on all Posix
      systems and on Windows Vista / 2008 and higher. On Windows, managing
      symlinks requires puppet agent's user account to have the "Create
      Symbolic Links" privilege; this can be configured in the "User Rights
      Assignment" section in the Windows policy editor. By default, puppet
      agent runs as the Administrator account, which does have this
    privilege.
    Puppet avoids destroying directories unless the `force` attribute is set
    to `true`. This means that if a file is currently a directory, setting
    `ensure` to anything but `directory` or `present` will cause Puppet to
    skip managing the resource and log either a notice or an error.
    There is one other non-standard value for `ensure`. If you specify the
    path to another file as the ensure value, it is equivalent to specifying
    `link` and using that path as the `target`:
        # Equivalent resources:
        file { "/etc/inetd.conf":
          ensure => "/etc/inet/inetd.conf",
        }
        file { "/etc/inetd.conf":
          ensure => link,
          target => "/etc/inet/inetd.conf",
        }
    However, we recommend using `link` and `target` explicitly, since this
    behavior can be harder to read.
    Valid values are `absent` (also called `false`), `file`, `present`,
    `directory`, `link`. Values can match `/./`.

- **force**
    Perform the file operation even if it will destroy one or more
    directories.
    You must use `force` in order to:
    * `purge` subdirectories
    * Replace directories with files or links
    * Remove a directory when `ensure => absent`
    Valid values are `true`, `false`, `yes`, `no`. 

- **group**
    Which group should own the file.  Argument can be either a group
    name or a group ID.
    On Windows, a user (such as "Administrator") can be set as a file's
    group
    and a group (such as "Administrators") can be set as a file's owner;
    however, a file's owner and group shouldn't be the same. (If the owner
    is also the group, files with modes like `0640` will cause log churn, as
    they will always appear out of sync.)

- **ignore**
    A parameter which omits action on files matching
    specified patterns during recursion.  Uses Ruby's builtin globbing
    engine, so shell metacharacters are fully supported, e.g. `[a-z]*`.
    Matches that would descend into the directory structure are ignored,
    e.g., `*/*`.

- **links**
    How to handle links during file actions.  During file copying,
    `follow` will copy the target file instead of the link, `manage`
    will copy the link itself, and `ignore` will just pass it by.
    When not copying, `manage` and `ignore` behave equivalently
    (because you cannot really ignore links entirely during local
    recursion), and `follow` will manage the file to which the link points.
    Valid values are `follow`, `manage`. 

- **mode**
    The desired permissions mode for the file, in symbolic or numeric
    notation. Puppet uses traditional Unix permission schemes and translates
    them to equivalent permissions for systems which represent permissions
    differently, including Windows.
    Numeric modes should use the standard four-digit octal notation of
    `<setuid/setgid/sticky><owner><group><other>` (e.g. 0644). Each of the
    "owner," "group," and "other" digits should be a sum of the
    permissions for that class of users, where read = 4, write = 2, and
    execute/search = 1. When setting numeric permissions for
    directories, Puppet sets the search permission wherever the read
    permission is set.
    Symbolic modes should be represented as a string of comma-separated
    permission clauses, in the form `<who><op><perm>`:
    * "Who" should be u (user), g (group), o (other), and/or a (all)
    * "Op" should be = (set exact permissions), + (add select permissions),
      or - (remove select permissions)
    * "Perm" should be one or more of:
        * r (read)
        * w (write)
        * x (execute/search)
        * t (sticky)
        * s (setuid/setgid)
        * X (execute/search if directory or if any one user can execute)
        * u (user's current permissions)
        * g (group's current permissions)
        * o (other's current permissions)
    Thus, mode `0664` could be represented symbolically as either `a=r,ug+w`
    or `ug=rw,o=r`.  However, symbolic modes are more expressive than
    numeric
    modes: a mode only affects the specified bits, so `mode => 'ug+w'` will
    set the user and group write bits, without affecting any other bits.
    See the manual page for GNU or BSD `chmod` for more details
    on numeric and symbolic modes.
    On Windows, permissions are translated as follows:
    * Owner and group names are mapped to Windows SIDs
    * The "other" class of users maps to the "Everyone" SID
    * The read/write/execute permissions map to the `FILE_GENERIC_READ`,
      `FILE_GENERIC_WRITE`, and `FILE_GENERIC_EXECUTE` access rights; a
      file's owner always has the `FULL_CONTROL` right
    * "Other" users can't have any permissions a file's group lacks,
      and its group can't have any permissions its owner lacks; that is,
    0644
  is an acceptable mode, but 0464 is not.

- **mtime**
    A read-only state to check the file mtime. On \*nix-like systems, this
    is the time of the most recent change to the content of the file.

- **owner**
    The user to whom the file should belong.  Argument can be a user name or
    a
    user ID.
    On Windows, a group (such as "Administrators") can be set as a file's
    owner
    and a user (such as "Administrator") can be set as a file's group;
    however,
    a file's owner and group shouldn't be the same. (If the owner is also
    the group, files with modes like `0640` will cause log churn, as they
    will always appear out of sync.)

- **path** (*namevar*)
    The path to the file to manage.  Must be fully qualified.
    On Windows, the path should include the drive letter and should use `/`
    as
the separator character (rather than `\\`).

- **purge**
    Whether unmanaged files should be purged. This option only makes
    sense when managing directories with `recurse => true`.
    * When recursively duplicating an entire directory with the `source`
      attribute, `purge => true` will automatically purge any files
      that are not in the source directory.
    * When managing files in a directory as individual resources,
      setting `purge => true` will purge any files that aren't being
      specifically managed.
    If you have a filebucket configured, the purged files will be uploaded,
    but if you do not, this will destroy data.
    Valid values are `true`, `false`, `yes`, `no`. 

- **recurse**
    Whether and how to do recursive file management. Options are:
    * `inf,true` --- Regular style recursion on both remote and local
      directory structure.  See `recurselimit` to specify a limit to the
      recursion depth.
    * `remote` --- Descends recursively into the remote (source) directory
      but not the local (destination) directory. Allows copying of
      a few files into a directory containing many
      unmanaged files without scanning all the local files.
      This can only be used when a source parameter is specified.
    * `false` --- Default of no recursion.
    Valid values are `true`, `false`, `inf`, `remote`. 

- **recurselimit**
    How deeply to do recursive management.
Values can match `/^[0-9]+$/`.

- **replace**
    Whether to replace a file or symlink that already exists on the local
    system but
    whose content doesn't match what the `source` or `content` attribute
    specifies.  Setting this to false allows file resources to initialize
    files
    without overwriting future changes.  Note that this only affects
    content;
    Puppet will still manage ownership and permissions. Defaults to `true`.
    Valid values are `true`, `false`, `yes`, `no`. 

- **selinux_ignore_defaults**
    If this is set then Puppet will not ask SELinux (via matchpathcon) to
    supply defaults for the SELinux attributes (seluser, selrole,
    seltype, and selrange). In general, you should leave this set at its
    default and only set it to true when you need Puppet to not try to fix
    SELinux labels automatically.
Valid values are `true`, `false`. 

- **selrange**
    What the SELinux range component of the context of the file should be.
    Any valid SELinux range component is accepted.  For example `s0` or
    `SystemHigh`.  If not specified it defaults to the value returned by
    matchpathcon for the file, if any exists.  Only valid on systems with
    SELinux support enabled and that have support for MCS (Multi-Category
    Security).

- **selrole**
    What the SELinux role component of the context of the file should be.
    Any valid SELinux role component is accepted.  For example `role_r`.
    If not specified it defaults to the value returned by matchpathcon for
    the file, if any exists.  Only valid on systems with SELinux support
    enabled.

- **seltype**
    What the SELinux type component of the context of the file should be.
    Any valid SELinux type component is accepted.  For example `tmp_t`.
    If not specified it defaults to the value returned by matchpathcon for
    the file, if any exists.  Only valid on systems with SELinux support
    enabled.

- **seluser**
    What the SELinux user component of the context of the file should be.
    Any valid SELinux user component is accepted.  For example `user_u`.
    If not specified it defaults to the value returned by matchpathcon for
    the file, if any exists.  Only valid on systems with SELinux support
    enabled.

- **show_diff**
    Whether to display differences when the file changes, defaulting to
    true.  This parameter is useful for files that may contain passwords or
    other secret data, which might otherwise be included in Puppet reports
    or
    other insecure outputs.  If the global `show_diff` setting
    is false, then no diffs will be shown even if this parameter is true.
    Valid values are `true`, `false`, `yes`, `no`. 

- **source**
    A source file, which will be copied into place on the local system.
    Values can be URIs pointing to remote files, or fully qualified paths to
    files available on the local system (including files on NFS shares or
    Windows mapped drives). This attribute is mutually exclusive with
    `content` and `target`.
    The available URI schemes are *puppet* and *file*. *Puppet*
    URIs will retrieve files from Puppet's built-in file server, and are
    usually formatted as:
    `puppet:///modules/name_of_module/filename`
    This will fetch a file from a module on the puppet master (or from a
    local module when using puppet apply). Given a `modulepath` of
    `/etc/puppetlabs/puppet/modules`, the example above would resolve to
    `/etc/puppetlabs/puppet/modules/name_of_module/files/filename`.
    Unlike `content`, the `source` attribute can be used to recursively copy
    directories if the `recurse` attribute is set to `true` or `remote`. If
    a source directory contains symlinks, use the `links` attribute to
    specify whether to recreate links or follow them.
    Multiple `source` values can be specified as an array, and Puppet will
    use the first source that exists. This can be used to serve different
    files to different system types:
        file { "/etc/nfs.conf":
          source => [
            "puppet:///modules/nfs/conf.$host",
            "puppet:///modules/nfs/conf.$operatingsystem",
            "puppet:///modules/nfs/conf"
          ]
        }
    Alternately, when serving directories recursively, multiple sources can
    be combined by setting the `sourceselect` attribute to `all`.

- **source_permissions**
    Whether (and how) Puppet should copy owner, group, and mode permissions
    from
    the `source` to `file` resources when the permissions are not explicitly
    specified. (In all cases, explicit permissions will take precedence.)
    Valid values are `use`, `use_when_creating`, and `ignore`:
    * `use` (the default) will cause Puppet to apply the owner, group,
      and mode from the `source` to any files it is managing.
    * `use_when_creating` will only apply the owner, group, and mode from
    the
      `source` when creating a file; existing files will not have their
    permissions
      overwritten.
    * `ignore` will never apply the owner, group, or mode from the `source`
    when
      managing a file. When creating new files without explicit permissions,
      the permissions they receive will depend on platform-specific
    behavior.
      On POSIX, Puppet will use the umask of the user it is running as. On
      Windows, Puppet will use the default DACL associated with the user it
    is
  running as.
Valid values are `use`, `use_when_creating`, `ignore`. 

- **sourceselect**
    Whether to copy all valid sources, or just the first one.  This
    parameter
    only affects recursive directory copies; by default, the first valid
    source is the only one used, but if this parameter is set to `all`, then
    all valid sources will have all of their contents copied to the local
    system. If a given file exists in more than one source, the version from
    the earliest source in the list will be used.
    Valid values are `first`, `all`. 

- **target**
    The target for creating a link.  Currently, symlinks are the
    only type supported. This attribute is mutually exclusive with `source`
    and `content`.
    Symlink targets can be relative, as well as absolute:
        # (Useful on Solaris)
        file { "/etc/inetd.conf":
          ensure => link,
          target => "inet/inetd.conf",
        }
    Directories of symlinks can be served recursively by instead using the
    `source` attribute, setting `ensure` to `directory`, and setting the
    `links` attribute to `manage`.
    Valid values are `notlink`. Values can match `/./`.

- **type**
    A read-only state to check the file type.

- **validate_cmd**
    A command for validating the file's syntax before replacing it. If
    Puppet would need to rewrite a file due to new `source` or `content`, it
    will check the new content's validity first. If validation fails, the
    file
    resource will fail.
    This command must have a fully qualified path, and should contain a
    percent (`%`) token where it would expect an input file. It must exit
    `0`
    if the syntax is correct, and non-zero otherwise. The command will be
    run on the target system while applying the catalog, not on the puppet
    master.
    Example:
        file { '/etc/apache2/apache2.conf':
          content      => 'example',
          validate_cmd => '/usr/sbin/apache2 -t -f %',
        }
    This would replace apache2.conf only if the test returned true.
    Note that if a validation command requires a `%` as part of its text,
    you can specify a different placeholder token with the
    `validate_replacement` attribute.

- **validate_replacement**
    The replacement string in a `validate_cmd` that will be replaced
    with an input file name. Defaults to: `%`

Providers
---------
    posix, windows
```