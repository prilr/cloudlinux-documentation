# Features

Below you can find the list of supported features with the links to the documentation available.

## LVE limits

* [Understanding limits](/cloudlinuxos/limits/#understanding-limits)
* [SPEED limit](/cloudlinuxos/limits/#speed-limit)
* [Memory limit](/cloudlinuxos/limits/#memory-limit)
* [IO](/cloudlinuxos/limits/#io)
* [IOPS](/cloudlinuxos/limits/#iops)
* [Entry processes](/cloudlinuxos/limits/#entry-processes)
* [Number of processes](/cloudlinuxos/limits/#number-of-processes)
* [Inodes](/cloudlinuxos/limits/#inodes)
* [Network traffic bandwidth control and accounting system](/cloudlinuxos/limits/#network-traffic-bandwidth-control-and-accounting-system)
* [Limits validation](/cloudlinuxos/limits/#limits-validation)
* [Compatibility matrix](/cloudlinuxos/limits/#compatibility-matrix)
* [Reseller limits](/cloudlinuxos/limits/#reseller-limits)

## Inode limits

The documentation is available [here](/cloudlinuxos/limits/#inodes).

## Reseller limits

The documentation is available [here](/cloudlinuxos/limits/#reseller-limits).

## MySQL Governor

MySQL Governor is a software package that monitors and restricts MySQL usage in a shared hosting environment. The monitoring is done via resource usage statistics per each MySQL thread.

MySQL Governor can also kill off slow SELECT queries.

MySQL Governor has multiple modes of operations, depending on the configuration. It can work in monitor-only mode, or it can use different throttling scenarios.

MySQL Governor allows restricting customers that use too many resources. It supports following limits:

|       |       |                                                                                                        |
|-------|-------|--------------------------------------------------------------------------------------------------------|
| CPU   | %     | CPU speed relative to one core. 150% would mean one and a half cores                                   |
| READ  | bytes | bytes read. Cached reads are not counted, only those that were actually read from disk will be counted |
| WRITE | bytes | bytes written. Cached writes are not counted, only once data is written to disk, it is counted         |

You can set different limits for different periods: current, short, mid, long. By default those periods are defined as 1 second, 5 seconds, 1 minute and 5 minutes. They can be re-defined using the [configuration file](/cloudlinuxos/cloudlinux_os_components/#configuration-and-operation).
The idea is to use larger acceptable values for shorter periods. Like you could allow a customer to use two cores (200%) for one second, but only 1 core (on average) for 1 minute, and only 70% within 5 minutes.
That would make sure that customer can burst for short periods of time.

When a customer is restricted, the customer will be placed into special LVE with ID 3. All restricted customers will be
placed into that LVE, and you can control the amount of resources available to restricted customers. Restricted
customers will also be limited to only 30 concurrent connections. This is done so they wouldn't use up all the MySQL
connections to the server.

### Installation

:::warning Attention!
MySQL Governor on Ubuntu supports the following only:

* cl-MySQL80 on non-panel systems
* cl-MySQL80 on cPanel
:::

1. Install MySQL Governor

```
apt  install governor-mysql
```

2. To use MySQL Governor with

    * cl-MySQL80
    ```
    /usr/share/lve/dbgovernor/mysqlgovernor.py --mysql-version=mysql80
    ```
3. Backup your databases.
4. Run the cl-MySQL/cl-MariaDB installation.

```
/usr/share/lve/dbgovernor/mysqlgovernor.py --install --yes
```

In case of installing on cPanel + Ubuntu server, set the following parameter:

![](/images/ubuntu/features/Param.webp)

5. After installation, check that the database server is working properly. If you have any problems,
   use [Support Portal]().
6. Configure user mapping to the database. The mapping format is described in
   the [following section](/cloudlinuxos/cloudlinux_os_components/#mapping-a-user-to-a-database).

In case of a non-panel system the `/etc/container/dbuser-map` mapping file should be created and updated with new users by an admin.

The format is as follows:

```
[dbuser_name1] [account_name1] [UID1]
...
[dbuser_nameN] [account_nameN] [UIDN]
```

The control panel should automatically generate this mapping and write it to the `/etc/container/dbuser-map` file. Usually,
it is enough to write a hook when adding, deleting or renaming a database for a user. The control panel should implement
such a mechanism for MySQL Governor to operate properly. MySQL Governor automatically applies changes from the
dbuser-map file every five minutes.

7. MySQL Governor configuration can be found in the
   following [section](/cloudlinuxos/cloudlinux_os_components/#configuration-3).
8. MySQL Governor CLI tools description can be found in the
   following [section](/cloudlinuxos/command-line_tools/#mysql-governor).
9. Having configured the mapping use `dbtop` to see the current user load on the database (you'd need to make some
   database queries).

```
dbtop
```

10. If the load appears in the dbtop output, then you have successfully configured MySQL Governor.

### How To upgrade database server

You can find the instructions
in [this documentation](/cloudlinuxos/cloudlinux_os_components/#upgrading-database-server).

### Uninstalling

To remove MySQL Governor, run the following command:

```
/usr/share/lve/dbgovernor/mysqlgovernor.py --delete
```

The script will install the original MySQL server, and remove MySQL Governor.

### Configuration and operation

You can find the instructions
in [this documentation](/cloudlinuxos/cloudlinux_os_components/#configuration-and-operation).

## Administrator WEB interface (CloudLinux Manager)

The documentation is available [here](/cloudlinuxos/lve_manager/#cloudlinux-manager).

* [Dashboard](/sub-system-ubuntu/features/#dashboard)
* [Current Usage](/cloudlinuxos/lve_manager/#current-usage)
* [Users](/cloudlinuxos/lve_manager/#users)
* [Statistics](/cloudlinuxos/lve_manager/#statistics)
* [Packages](/cloudlinuxos/lve_manager/#packages)
* [Options](/cloudlinuxos/lve_manager/#options)
* [PHP Selector](/cloudlinuxos/cloudlinux_os_components/#php-selector)
    * [Installation instructions](/features/#php-selector-installation-instructions)
    * [Selector tab](/cloudlinuxos/lve_manager/#selector-tab)
    * [Selector tab additional features (cPanel)](/cloudlinuxos/lve_manager/#selector-tab-additional-features)
      CloudLinux Manager 6.0.1-2
    * [PHP Selector troubleshooting (cPanel)](/cloudlinuxos/lve_manager/#php-selector-troubleshooting)
      CloudLinux Manager 6.0.1-2
    * [PHP Selector diagnostic tool and notifications](/cloudlinuxos/lve_manager/#php-selector-diagnostic-tool-and-notifications)
      CloudLinux Manager 6.0.1-2

### Dashboard

Dashboard provides a quick overview of statistics and all administrative information for server administrators.

Go to _CloudLinux Manager_ | _Dashboard_.

![](/images/ubuntu/features/Dashboard.webp)

* End Users hitting limits — number of users reaching their limit in any kind of resource. Data is within the last 24
  hours.
* Resellers hitting limits — number of enrolled Resellers that are reaching their limit in any kind of resource. Data is
  within the last 24 hours.
* [PHP Selector](/cloudlinuxos/lve_manager/#php-selector-2) block displays the following information:
    * Default version — the default version of PHP binaries. Click _Manage_ to change the default version, enable or
      disable PHP Selector, change the list of supported versions, and choose default modules. You will be redirected to
      CloudLinux Manager | PHP Selector. PHP Selector (cPanel) has malfunctions warnings
      about [the most common issues](/cloudlinuxos/lve_manager/#errors).
* [CageFS](/cloudlinuxos/cloudlinux_os_components/#cagefs) block displays the following information:
    * CageFS status (Enabled/Disabled/Not installed). To manage CageFS, click _Manage_. You will be redirected to
      CloudLinux Manager | Options | CageFS. Click _Install_ to install CageFS.
    * Mode displays the current CageFS mode of operation.
    * End users — displays the number of users with CageFS enabled/all.

## Reseller WEB interface (CloudLinux Manager)

* [Current Usage](/cloudlinuxos/lve_manager/#current-usage-tab)
* [Historical Usage](/cloudlinuxos/lve_manager/#historical-usage-tab)
* [Users](/cloudlinuxos/lve_manager/#users-tab)
* [Statistics](/cloudlinuxos/lve_manager/#statistics-tab)
* [Packages](/cloudlinuxos/lve_manager/#packages-tab)

## User WEB interface

* [Resource Usage](/cloudlinuxos/lve_manager/#resource-usage-client-plugin)
* [PHP Selector](/cloudlinuxos/lve_manager/#php-selector-client-plugin)

## LVE Wrappers

The documentation is available [here](/cloudlinuxos/cloudlinux_os_components/#lve-wrappers).

* lve_wrapper
* lve_suwrapper

## CageFS and running commands inside

The documentation is
available [here](/cloudlinuxos/control_panel_integration/#running-commands-inside-cagefs).

* cagefs_enter
* cagefs_enter_user

## Hardened PHP

* alt-php versions 5.5 - 8.2

## Mod_hostinglimits

LVE is a kernel level technology developed by the CloudLinux team. The technology has common roots with container based
virtualization and uses cgroups in its latest incarnation. It is lightweight and transparent. The goal of LVE is to make
sure that no single web site can bring down your web server.

Today, a single site can consume all CPU, IO, Memory resources or Apache processes - and bring the server to a halt. LVE
prevents that. It is done via collaboration of Apache module, PAM module and kernel.

[mod_hostinglimits](/cloudlinuxos/cloudlinux_os_components/#hostinglimits-module-for-apache) is an Apache
module that:

* detects VirtualHost from which the request came;
* detects if it was meant for CGI or PHP script;
* puts Apache process used to serve that request into LVE for the user determined via SuexecUserGroup directive for that
  virtual host;
* lets Apache to serve the request;
* removes Apache process from user's LVE.

The kernel makes sure that all LVEs get fair share of the server's resources, and that no customer can use more then the
limits set for that customer. Today we can limit CPU , Memory (virtual and physical), IO, number of processes as well as
the number of entry processes (concurrent connections to apache).

Each LVE limits amount of entry processes (Apache processes entering into LVE) to prevent single site exhausting all
Apache processes. If the limit is reached, then mod_hostinglimits will not be able to place Apache process into LVE, and
will return error code 508. This way very heavy site would slow down and start returning 508 errors, without affecting
other users.

* If the site is limited by CPU or IO, then the site will start responding slower.
* If the site is limited by memory or number of processes limits, then the user will receive 500 or 503 errors that
  server cannot execute the script.

#### ea-php81 with php-fpm

To install, run the following command:

```
apt install ea-php81*
```

#### alt-php with suphp, cgi

To install, follow these steps:

1. Remove `mod-ruid2`:

   ![](/images/ubuntu/features/remove-mod-ruid2.webp)

2. Install packages (with `alt-php74` as an example):

   ```
   apt install ea-apache24-mod-suphp ea-apache24-mod-suexec
   apt install alt-php74
   ```

#### PHP Selector installation instructions

The documentation is available [here](/cloudlinuxos/cloudlinux_os_components/#php-selector).

To install, run the following commands:

```
apt install ea-apache24-mod-suphp ea-apache24-mod-suexec
apt install cagefs
cagefsctl –-init
```

## Symlink owner match protection

The documentation is available [here](/cloudlinuxos/cloudlinux_os_kernel/#fs-enforce-symlinksifowner).

* [fs.enforce_symlinksifowner](/cloudlinuxos/cloudlinux_os_kernel/#fs-enforce-symlinksifowner)
* [fs.symlinkown_gid](/cloudlinuxos/cloudlinux_os_kernel/#fs-symlinkown-gid)
* [fs.process_symlinks_by_task](/cloudlinuxos/cloudlinux_os_kernel/#fs-process-symlinks-by-task)

## Link traversal protection

* [fs.protected_symlinks_create](/cloudlinuxos/cloudlinux_os_kernel/#link-traversal-protection)
* [fs.protected_hardlinks_create](/cloudlinuxos/cloudlinux_os_kernel/#link-traversal-protection)

## Tuned profiles

The documentation is available [here](/cloudlinuxos/cloudlinux_os_kernel/#tuned-profiles-cloudlinux).

## Apache mod_lsapi

### General requirements

General requirements are
available [here](/cloudlinuxos/cloudlinux_os_components/#general-information-and-requirements-9).

### Installation

Installation on cPanel servers with EasyApache 4:

```
apt install liblsapi liblsapi-dev 
apt install ea-apache24-mod-lsapi
/usr/bin/switch_mod_lsapi --setup
service httpd restart
```

Installation on servers with no panel:

```
apt install liblsapi liblsapi-dev 
apt install mod-lsapi
/usr/bin/switch_mod_lsapi --setup
service apache2 restart
```

### Configuration

Configuration instructions are available [here](/cloudlinuxos/cloudlinux_os_components/#configuration-4).

### Troubleshooting

In case the site responds with the error: 503 Service unavailable.

1. Be sure that /opt has drwxr-xr-x permissions. It can be fixed with the following command:

```
chmod 755 /opt 
```

2. Change the default folder for mod_lsapi socket:

```
mkdir /var/mod_lsapi
chown nobody.nobody /var/mod_lsapi
chmod 755 /var/mod_lsapi
```

Add to /etc/apache2/conf.d/lsapi.conf new path:

```
lsapi_socket_path /var/mod_lsapi
```

And restart service:

```
service httpd restart
```

### Uninstallation

Uninstallation procedure for cPanel servers with EasyApache 4:

```
/usr/bin/switch_mod_lsapi --uninstall
apt remove liblsapi liblsapi-dev ea-apache24-mod-lsapi
```

Uninstallation procedure for servers with no panel:

```
/usr/bin/switch_mod_lsapi --uninstall
apt remove liblsapi liblsapi-dev mod-lsapi
```
