# Setting up a cpu server

**Notation**: Some commands in this document begin with a semicolon `; `.  
This is to indicate the prompt input, to distinguish between rc commands,
and following user interaction with running programs. 

## Before installation
It might be beneficial to set up a temporary cpu service, as this will let you 
access the installer from a client software such as 
[`rcpu(1)`](http://man.9front.org/1/rcpu), [`drawterm(1)`](http://drawterm.9front.org/) 
or [tlsclient](https://git.sr.ht/~moody/tlsclient).
This allows copy-and-pasting of commands.  
`rcpu` is the Plan 9 equivalent of `ssh` or `telnet`, 
which allows you to remotely open an `rc` shell session; 
and `drawterm` is akin to a remote desktop.  
rcpu is only available on 9front.

If the machine does not have network access, it can be set up using 
`ip/ipconfig ether /net/ether0`, which will configure the network using DHCP. 
Verify the machine has network using `ip/ping`. 
The IP address of the machine can be printed by `cat /net/ndb`.

Once the machine has network access, we can set up a temporary cpu service with

	; echo 'key proto=dp9ik dom=9front user='$user' !password=BADBADDIE' >/mnt/factotum/ctl
	; aux/listen1 -t 'tcp!*!rcpu' /rc/bin/service/tcp17019

You can now connect to the machine using `drawterm -h <ip address> -u glenda`

## Install to disk
Install 9front onto disk with `inst/start`, then reboot into the newly installed system.  
Installation instructions are available at [fqa4](fqa.9front.org/fqa4.html).

The following steps are written assuming cwfs(4) was selected as the file server, 
and the machine has basic networking.

## Create new user
A new user can be added to the fileserver by sending 
the following commands to /srv/cwfs.cmd.

First, open a console into `/srv/cwfs.cmd`.

	; con -C /srv/cwfs.cmd

Then, issue the following commands to create a new user, 
and add it to common groups.

	newuser alice
	newuser sys +alice
	newuser adm +alice
	newuser upas +alice

Leave the console by pressing `Ctrl-\` and pressing enter, then q and enter.

- Most of the filesystem is owned by `sys`, 
being a member of this group grants permissions to manage /sys/src and other resources.
- `adm` group allows access to /adm, which stores auth server keys, timezones, 
and the users file.
- `upas` is the group for mail server. it allows delivery of mail to your mailbox.

Now, you can reboot the machine with `fshalt -r` in order to change user, then run

	; /sys/lib/newuser

as the new user to set up the home directory with default settings.

To complete the following steps, you should reboot again and log in as *glenda*.

## Setting up the CPU service
To start the cpu service at boot, edit `plan9.ini` 
and append the following line to it:

	service=cpu

This kernel parameter starts the machine as a cpu server 
rather than a terminal/workstation.
	
`plan9.ini` is at the 9fat partition, and can be accessed using

	; 9fs 9fat
	; acme /n/9fat/plan9.ini

---

Next we'll store the hostowner authentication credentials.
These must be stored in the NVRAM, a special partition which stores data for factotum. 
We can write to it with the `auth/wrkey(8)` command.

If you are netbooting, this information allows the cpu server to mount a remote 
filesystem without manual interaction. It also serves as a fallback, 
if for any reason the auth server is non-functioning, you can still login using 
the auth information stored in the factotum from nvram.

**Note**: it is important to enter the following information correctly. 
The information you will provide here will be required in subsequent steps when 
creating an auth user and configuring the auth server.

It is good practice to explicitly specify the nvram partition using 
the environment variable `nvram` when calling `auth/wrkey`. 
The available partitions can be listed by running `ls /dev/sd*/`

	; nvram=/dev/sdF0/nvram auth/wrkey
	bad nvram des key
	bad authentication id
	bad authentication domain
	authid: alice
	authdom: plan9.local
	secstore key: <press enter to skip>
	password: <type your password>
	confirm pasword: <retype your password>
	enable legacy p9sk1[no]: 

The secstore key allows the cpu server to load additional information such as 
ssh keys or tls secrets into factorum from `secstored`, which is only needed if
 one plans to use secstored. It can be safely skipped here.

**Note**: it is discouraged to enable the legacy p9sk1 protocol.
It is used to interact with non-9front Plan 9 machines, 
and is considered vulnerable for using DES encryption.

## Setting up the auth server
An authentication server needs to be set up to allow for users to log in.

In order for the machine to run authsrv(6), we must define it as an auth server 
in the ndb. A typical auth server is intended to be used by a cluster of machines 
in a particular subnet.
Adding the following to `/lib/ndb/local` defines that `nodename` be used as the 
auth server for every machine in the 192.168.0.0/24 subnet.

	ipnet=plan9.local ip=192.168.0.0 ipmask=255.255.255.0
		auth=nodename authdom=plan9.local

These names can be changed to your liking, however our auth domain has to be the 
same as the one stored in nvram by `auth/wrkey` in the preceeding step.

### Add users to auth server
Now we can add users to the auth server.
For this, we start a temporary `keyfs(4)`, 
then use the `auth/changeuser(8)` command to add users:

	; auth/keyfs
	; auth/changeuser alice
	Password: <same as what's in nvram>
	Confirm password: 
	assign new Inferno/POP secret? [y/n]: n
	Expiration date (YYYYMMDD or never)[never]: 
	Post id: 
	User's full name: 
	Department #: 
	User's email address: 
	Sponsor's email address: 
	user alice installed for Plan 9

The final step involves letting our new user "speak for" other users.
This is neccessary if the hostowner of the machine running the auth server is 
someone other than `glenda`, which is the case here.

Add the following entry to the file `/lib/ndb/auth`:

	hostid=alice
		uid=!sys uid=!adm uid=*

## Hands-free booting (optional)
By default, 9front will prompt for several parameters like the disk partition to mount, 
user name and password, and possibly some others.
This is may be a problem for headless CPU servers 
with no one present at the console to confirm the defaults.  
These parameters can be supplied without user interaction with 
the following changes:

First, we want to skip the `bootargs` prompt asking us to 
select the partition to boot from.  
This is done by changing the `plan9.ini` key `bootargs=` to `nobootprompt=`; 
the value stays the same.

	bootargs=local!/dev/sd..
	# change to...
	nobootprompt=local!/dev/sd..

Finally, add the following line to change the default user

	user=alice

## Reboot

	fshalt -r

After reboot, the machine should bring itself up without user interaction 
and allow you to connect to it using `drawterm` or `rcpu`.
