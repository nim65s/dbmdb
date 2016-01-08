title: Let us play with D-Bus \o/ (quick example with python and spotify)
date: 2014-07-03
author: Nim
sites: saurel.me

> D-Bus is a free and open-source inter-process communication (IPC) system, allowing multiple, concurrently-running computer programs (processes) to communicate with one another.

But if you are here, you probably already know what it is, and you may want to know how to quickly use it to talk with your applications.

You have probably already heard about tools like [dbus-send](http://dbus.freedesktop.org/doc/dbus-send.1.html) or [qdbus](http://manpages.ubuntu.com/manpages/trusty/man1/qdbus.1.html), but you first need to know which messages you can send.

The solution's name is [D-Feet](https://wiki.gnome.org/action/show/Apps/DFeet). It's a graphical tool that allows you to seamlessly and easily use D-Bus introspection functions to quickly find which methods and properties are available for your applications.
If you need a console tool, you can also try mdbus2.

Now, one quick example: let's say that you would like to control your spotify client.

1. Launch Spotify
2. Type "spotify" in D-Feet's search field. If nothing appears, check that you are looking in the "Session Bus", and not the "System Bus"
3. You should easily find methods such as "Play", "Pause", "PlayPause", "Next", "Previous", "Stop" and a few other ones that are pretty self-explaining. Here, you can double clic on methods to call them, or on readable properties to read them.
4. Now launch a [python](http://ipython.org), with [dbus-python](http://www.freedesktop.org/wiki/Software/DBusBindings/#Python) installed (dbus-python &gt;= 1.0 works with py2 &amp; py3) and imported: `>>> import dbus`
5. Get an interface object

        :::python
        bus = dbus.SessionBus()
        proxy = bus.get_object('org.mpris.MediaPlayer2.spotify', '/org/mpris/MediaPlayer2')
        interface = dbus.Interface(proxy, dbus_interface='org.mpris.MediaPlayer2.Player')
Where `org.mpris.MediaPlayer2.spotify` is the "bus name" you can see on the left column of D-Feet, `/org/mpris/MediaPlayer2` is the "object path" in the first level of the tree in the right column, and `org.mpris.MediaPlayer2.Player` is the "dbus interface" which will allow you to directly call methods.

6. What now ? Nothing, you're done ! Wanna switch play/pause ? `interface.PlayPause()` ! Wanna stop ? `interface.Stop()`
7. Ok, ok, if you need to get or set properties, you have to use the getter and setter in the interface `org.freedesktop.DBus.Properties`:

        :::python
        props_iface = Interface(proxy, dbus_interface='org.freedesktop.DBus.Properties')
        props_iface.GetAll("org.mpris.MediaPlayer2.Player")

But it seems to be a really poor example because the spotify client just does not implement all the standard functionnalities of [org.mpris.MediaPlayer2](http://specifications.freedesktop.org/mpris-spec/latest/):( (tried `interface.Play()` ? Sorry :P)

Oh, and three more things:

* You can find my script in py3 on [github](https://github.com/nim65s/scripts/blob/master/spotify.py)
* if you really want bash:

        dbus-send --print-reply --dest=org.mpris.MediaPlayer2.spotify \
            /org/mpris/MediaPlayer2 org.mpris.MediaPlayer2.Player.PlayPause

* Another example to connect your bluetooth device: [github](https://github.com/nim65s/scripts/blob/master/bt.py)

