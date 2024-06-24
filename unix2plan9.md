# UNIX to Plan 9 translation

What follows is a table of transliterations from UNIX to Plan 9 commands
or syntactical elements of Bourne-like shells to the Plan 9 rc shell.

Plan 9 commands are typically not direct ports of UNIX commands and
as such should not be expected to operate the same.

When in doubt, UNIX refers to your average GNU/Linux distribution and
a Bourne-like shell, most likely _bash(1)_. Similarly, when in doubt, Plan 9
refers to the 9front fork of Plan 9.

## Commands

### Tools

<table>
	<tr>
		<th>UNIX</th>
		<th>Plan 9</th>
	</tr><tr>
		<td><code>sh</code></td>
		<td><code>rc</code> or <code>ape/psh</code></td>
	</tr><tr>
		<td><code>apropros</code> or <code>man -k</code></td>
		<td><code>lookman</code></td>
	</tr><tr>
		<td><code>at specific-time</code></td>
		<td><code>while (! ~ `{date} specific-time){ commands }</code></td>
	</tr><tr>
		<td><code>cc</code></td>
		<td><code>ape/cc</code> or the native [2c](http://man.cat-v.org/9front/1/2c) suite</td>
	</tr><tr>
		<td><code>ld</code></td>
		<td>The native [2l](http://man.cat-v.org/9front/1/2l) suite</td>
	</tr><tr>
		<td><code>make</code></td>
		<td><code>mk</code></td>
	</tr><tr>
		<td><code>less</code> and <code>more</code></td>
		<td><code>p</code></td>
	</tr><tr>
		<td><code>which</code></td>
		<td><code>whatis</code></td>
	</tr><tr>
		<td><code>doas</code></td>
		<td><code>auth/as</code></td>
	</tr><tr>
		<td><code>yes</code></td>
		<td><code>while(){ echo y }</code></td>
	</tr><tr>
		<td><code>shutdown</code></td>
		<td><code>fshalt</code></td>
	</tr><tr>
		<td><code>reboot</code></td>
		<td><code>fshalt -r</code> or <code>reboot</code></td>
	</tr><tr>
		<td><code>startx</code></td>
		<td><code>rio</code></td>
	</tr><tr>
		<td><code>top</code></td>
		<td><code>stats</code> and <code>ps</code></td>
	</tr><tr>
		<td><code>tree</code></td>
		<td><code>du $* | awk '{print $2}' | sort | sed 's/[^\/]+\//	/g'</code></td>
	</tr><tr>
		<td><code>ufs{dump|restore}</code></td>
		<td><code>yesterday</code>, <code>history</code>, or <code>9fs dump</code></td>
	</tr><tr>
		<td><code>mail</code></td>
		<td><code>upas/fs</code>, <code>mail</code>, and <code>faces</code></td>
	</tr><tr>
		<td><code>tcpdump</code></td>
		<td><code>snoopy</code></td>
	</tr><tr>
		<td><code>ping</code></td>
		<td><code>ip/ping</code></td>
	</tr><tr>
		<td><code>apt-get dist-upgrade</code>, <code>rpm -Ua</code>, <code>yum -c update</code></td>
		<td><code>sysupdate && cd /sys/src && mk clean && mk libs && mk install</code></td>
	</tr><tr>
		<td><code>apt</code>, <code>yum</code>, <code>pacman</code></td>
		<td>See ExternalSoftware</td>
	</tr><tr>
		<td><code>ftp</code></td>
		<td><code>ftpfs</code></td>
	</tr><tr>
		<td><code>ftpd</code></td>
		<td><code>aux/listen ftp</code></td>
	</tr><tr>
		<td><code>httpd</code></td>
		<td><code>rc-httpd/rc-httpd</code> or <code>ip/httpd/httpd</code></td>
	</tr><tr>
		<td><code>head</code></td>
		<td><code>sed 11q</code></td>
	</tr><tr>
		<td><code>groff</code></td>
		<td><code>troff</code></td>
	</tr><tr>
		<td><code>groff -l</code></td>
		<td><code>troff | lp</code></td>
	</tr><tr>
		<td><code>grops</code></td>
		<td><code>dpost</code></td>
	</tr><tr>
		<td><code>getopt</code></td>
		<td><code>aux/getflags</code></td>
	</tr><tr>
		<td><code>vim</code>, <code>emacs</code>, <code>nano</code>, etc.</td>
		<td><code>sam</code>, <code>acme</code>, and <code>ed</code></td>
	</tr><tr>
		<td rowspan=2><code>df</code></td>
		<td>For hjfs: <code>echo df >> /srv/hjfs.cmd</code></td>
	</tr><tr>
		<td>For cwfs: <code>con -Cl /srv/cwfs.cmd</code>, then <code>stats</code></td>
	</tr><tr>
		<td><code>hwclock</code></td>
		<td><code>cat '#r/rtc'</code></td>
	</tr><tr>
		<td><code>ifconfig</code> or <code>ip</code></td>
		<td><code>ip/ipconfig</code></td>
	</tr><tr>
		<td><code>ip addr show</code></td>
		<td><code>cat /net/ndb</code> or <code>cat /net/ipselftab</code></td>
	</tr><tr>
		<td><code>kill -s STOP pid</code></td>
		<td><code>stop procname | rc</code> or <code>echo stop > /proc/pid/ctl</code></td>
	</tr><tr>
		<td><code>kill -s CONT pid</code></td>
		<td><code>start procname | rc</code> or <code>echo start > /proc/pid/ctl</code></td>
	</tr><tr>
		<td><code>kill -9 pid</code></td>
		<td><code>kill procname | rc</code>, <code>Kill procname | rc</code>, and <code>slay procname | rc</code></td>
	</tr><tr>
		<td><code>lspci</code></td>
		<td><code>pci -v</code> and <code>sysinfo</code></td>
	</tr><tr>
		<td><code>mount</code> and <code>unmount</code></td>
		<td><code>bind</code>, <code>mount</code>, <code>unmount</code>, <code>srv</code>, <code>import</code>, <code>exportfs</code>, etc.</td>
	</tr><tr>
		<td><code>ln</code></td>
		<td><code>bind</code></td>
	</tr><tr>
		<td><code>fpaste</code> or <code>pastebinit</code></td>
		<td><code>webpaste</code></td>
	</tr><tr>
		<td><code>nslookup</code></td>
		<td><code>ndb/dnsquery</code></td>
	</tr><tr>
		<td><code>paste [file]</code></td>
		<td><code>pr -m [file]</code></td>
	</tr><tr>
		<td><code>netstat</code></td>
		<td><code>netstat</code> or <code>cat /net/iproute</code></td>
	</tr><tr>
		<td><code>netcat -l</code></td>
		<td><code>aux/listen1 -t tcp!*!$port command</code></td>
	</tr><tr>
		<td><code>netcat google.com 80</code></td>
		<td><code>con -Cl tcp!google.com!80</code></td>
	</tr><tr>
		<td rowspan=2><code>ssh</code></td>
		<td>For other Plan 9 machines: <code>rcpu</code> and <code>srv</code></td>
	</tr><tr>
		<td>For UNIX machines: <code>ssh</code> and <code>sshfs</code></td>
	</tr><tr>
		<td><code>id</code> and <code>whoami</code></td>
		<td><code>whois $user</code></td>
	</tr><tr>
		<td rowspan=2><code>find</code></td>
		<td>By name: <code>du -a | grep pattern</code> and <code>grep pattern `{du -a root}</code></td>
	</tr><tr>
		<td>By content pattern: <code>grep -n pattern `{du -a root | awk '{print $2}'}</code></td>
	</tr><tr>
		<td><code>find -exec cp '{}' x ';'</code></td>
		<td><code>cp `{ du -a | grep pattern } x</code></td>
	</tr><tr>
		<td><code>cut</code></td>
		<td><code>awk -F …</code></td>
	</tr><tr>
		<td><code>crontab -e</code></td>
		<td><code>sam /cron/$user/cron</code></td>
	</tr><tr>
		<td><code>wget</code> and <code>curl</code></td>
		<td><code>hget</code> and <code>hpost</code></td>
	</tr><tr>
		<td><code>mount -t nfs $ip:/share /mnt/share</code></td>
		<td><code>nfs $ip</code></td>
	</tr><tr>
		<td><code>expr</code></td>
		<td><code>hoc -e</code></td>
	</tr><tr>
		<td><code>qemu-system-i386</code> and <code>qemu-system-x86_64</code></td>
		<td><code>vmx</code></td>
	</tr>
</table>

### GUI Programs ###

<table>
	<tr>
		<th>UNIX</th>
		<th>Plan 9</th>
	</tr><tr>
		<td><code>vlock</code></td>
		<td><code>screenlock</code></td>
	</tr><tr>
		<td><code>xwininfo</code></td>
		<td><code>winwatch</code></td>
	</tr><tr>
		<td><code>xclock</code></td>
		<td><code>games/catclock</code>, <code>clock</code>, or <code>faces</code></td>
	</tr><tr>
		<td><code>xload</code></td>
		<td><code>stats -l</code></td>
	</tr><tr>
		<td><code>xv file.jpg</code></td>
		<td><code>page file.jpg</code></td>
	</tr><tr>
		<td><code>xbiff</code></td>
		<td><code>faces</code></td>
	</tr><tr>
		<td><code>firefox</code>, etc.</td>
		<td><code>webfs</code> via <code>mothra</code> and <code>abaco</code></td>
	</tr>
</table>

## Shell Constructs

<table>
	<tr>
		<th>bash</th>
		<th>rc</th>
	</tr><tr>
		<td><code>export PATH=$PATH:/dir</code></td>
		<td><code>bind -a /dir /bin</code></td>
	</tr><tr>
		<td><code>`command`</code></td>
		<td><code>`{command}</code></td>
	</tr><tr>
		<td><code>~/</code></td>
		<td><code>$home/</code></td>
	</tr><tr>
		<td><code>"$@"</code></td>
		<td><code>$*</code></td>
	</tr><tr>
		<td><code>1>&2</code></td>
		<td><code>>[2=1]</code></td>
	</tr><tr>
		<td><code>source /file</code></td>
		<td><code>. /file</code></td>
	</tr><tr>
		<td><code>!!</code></td>
		<td><code>""</code></td>
	</tr>
</table>

## Files

<table>
	<tr>
		<th>UNIX</th>
		<th>Plan 9</th>
	</tr><tr>
		<td><code>~/.profile</code></td>
		<td><code>$home/lib/profile</code></td>
	</tr><tr>
		<td><code>/home</code></td>
		<td><code>/usr</code></td>
	</tr><tr>
		<td><code>/var/log</code></td>
		<td><code>/sys/log</code></td>
	</tr><tr>
		<td><code>/usr/lib</code></td>
		<td><code>/$objtype/lib</code></td>
	</tr><tr>
		<td><code>/usr/include</code></td>
		<td><code>/$objtype/include</code> and <code>/sys/include</code></td>
	</tr><tr>
		<td><code>/usr/bin</code></td>
		<td><code>/$objtype/bin</code></td>
	</tr><tr>
		<td><code>/boot</code></td>
		<td><code>9fs 9fat</code>, <code>/n/9fat</code></td>
	</tr>
</table>
