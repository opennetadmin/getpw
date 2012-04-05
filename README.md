WHAT IS GETPW
=============
getpw is a perl program that allows you to look up passwords for specified
devices based on a specified type of password.  The intent is to be used
by scripts that connect to other devices or services that require a password.

The password list that is returned can then be looped through by your script
until one is successful.

Install
=======
Since getpw is a perl script/program, and only needs to be copied to a directory
in your path to make it accessible.  Most likely the following steps will
be sufficient:

  * If you have not already, run the following command `echo '/opt/ona' > /etc/onabase`. This assumes you installed ONA into /opt/ona
  * Copy the getpw script to /opt/ona/bin or other binary location on the system
  * Make sure its executable: `chmod +x /opt/ona/bin/getpw`
  * Copy the getpw.conf.example configuration to `/opt/ona/etc/getpw.conf` and start adding passwords. 

   By default getpw will look in <onabase>/etc/getpw.conf for it's config file.
   If you do not have an /etc/onabase file then it will default to /etc/getpw.conf. 

NOTES
-----
  * getpw is written in Perl, so no compilation is needed.
  * On a Unix/Linux OS if your perl binary is not installed at /usr/bin/perl
    you may need to edit the first line of the script accordingly.
  * On a Microsoft OS you may need to put a .pl extension on getpw so
    Windows will know to associate it with perl.


USAGE
=====

Running getpw by itself will give you the usage synopsis.  It requires the -h
and -t options.

The -h option specifies the host name or ip address that you want to return a
password for.  If you specify a host name it will be first resolved to its IP
address before being matched in the password file.  It will use the first IP that
is found by the system resolver.

The -t option specifies the type of password you want returned.  It can be any
string that should ultimatly match one of the types specified in the password file.
Typical types include `enable`, `snmpro`, `snmprw`, etc.  You can use any type you
wish however.

MISC OPTIONS
------------

The -p option is used to select positionally which password you want to get back.  So
assuming that the specified password type has 4 password entries in the password file,
you could select the 3rd password using '-p 3'.  By default getpw returns the entire
comma separated list of password for the specified type.

The -v option will increase the debug output verbosity the more times it is used. -v for
debug level 1 or -vvv for debug level 3.


FAILURES
--------

Return codes can be tested via the shell variable $? and should be 0 on success
or anything greater than 1 on failure.  The return code should be a count of how 
many things failed.


PASSWORD FILE
-------------

!!!!!!!!!!!!!!!!!!!!!!!!!!!!IMPORTANT!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
BE CAREFUL not to let this password file be owned by the incorrect
group/user.

Remember that this file should only be readable by the people executing
`getpw` and processes that run `getpw`.

These passwords will only be as secure as the filesystem and permissions
that it is using.  This is much like the old `/etc/password` file in this
regard except that it will most likly be owned by something other than root.
!!!!!!!!!!!!!!!!!!!!!!!!!!!!IMPORTANT!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

The password file defines IP ranges and the password types that are valid for those
ranges.  The file is processed from top to bottom and will stop on the first
match it finds.

Each section is defined by an ip/mask statement surounded by square brackets.
You can specify a single host using its IP and a mask of 255.255.255.255.  You can
not specify masks in CIDR format (i.e. no /24 etc).  You can however specify
complex masks to mask out multiple cross sections of address space in one mask.

Examples:
    [10.0.0.0/255.0.0.0]
or
    [10.192.0.1/255.192.252.255]

Within the section you can define any password type and then its
resulting password values.  The type and the password values are seperated
by '=>'.  Each password value is seperated by a comma.  It is intended that
the passwords listed for each type are listed from newest to oldest.  This
allows other scripts to loop through the result and try each password in
order until one works.

Example:
    enable=>new-password1,old-password2,oldest-password3


WARNINGS/BUGS
-------------

I'll say it again:

!!!!!!!!!!!!!!!!!!!!!!!!!!!!IMPORTANT!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
BE CAREFUL not to let this password file be owned by the incorrect
group/user.

Remember that this file should only be readable by the people executing
`getpw` and processes that run `getpw`.

These passwords will only be as secure as the filesystem and permissions
that it is using.  This is much like the old `/etc/password` file in this
regard except that it will most likly be owned by something other than root.
!!!!!!!!!!!!!!!!!!!!!!!!!!!!IMPORTANT!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

It can be debated all day on the right way to do scripted/batch password
lookups.  I will never say that this method is the best.  It just serves
the purpose I have.  I'm comfortable relying on security of the filesystem
for what I use this for.  The basic assumption is that you know how to 
use file permissions and that the system you install this on is properly
secured at the OS level.

CONTRIBUTORS
------------

The team at my prior employer who helped come up with many ideas for this tool.

Brandon Zehm <http://caspian.dotconf.net>
   - Who I have patterned a lot of this code from.


WISHLIST
--------

-- It might be cool to also match passwords on name components.. like *.example.com or ptr[0-9].example.com...
