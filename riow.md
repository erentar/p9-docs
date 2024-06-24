[riow(1)](http://man.9front.org/1/riow) is a utility that allows interacting with rio in a similar manner to window managers such as i3 and sway.

In the manpage, Kmod4 is shown as the modifier. By default, this is the super key, also known as the "windows" key.

To start riow with rio at login, add

	#!/bin/rc
	</dev/kbdtap riow >/dev/kbdtap |[3] bar

to some startup script under any name you wish, here `riostart` is used

then, change `rio` to

	rio -i 'window riostart'

in $home/lib/profile
