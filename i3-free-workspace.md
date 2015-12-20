title: i3 free workspace
date: 2013-11-30
is_visible: 1
author: Nim
sites: saurel.me

A few years ago, I decided to give a try to some Tiling Window Manager. I began with Awesome, which was&hellip; Awesome. But sometimes, you want something new, and I tried XMonad. Great, but kind of hard to configure if you are not eating Haskell for your breakfast.

And a few days ago, I followed some friends and set up i3.

It is almost perfect for me, but it missed one important feature : beeing able to switch to (or move a container to) the next (or previous) free workspace. I think it is really cool to go to the next used workspace with <kbd>mod4</kbd> + <kbd>→</kbd>, but I needed my <kbd>mod4</kbd> +<kbd>ctrl</kbd> + <kbd>→</kbd>.

So, here is the quick-and-dirty script that let you do that <https://github.com/nim65s/scripts/blob/master/i3-free-workspace.py>

Then, in your .i3/config:

    bindsym $mod+Control+Left exec i3-free-workspace.py switch prev
    bindsym $mod+Control+Right exec i3-free-workspace.py switch next
    bindsym $mod+Control+Shift+Left exec i3-free-workspace.py move prev
    bindsym $mod+Control+Shift+Right exec i3-free-workspace.py move next

Well, with this quality of code, I don't think that it could help anybody, but why not ?
