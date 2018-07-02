# aussh

aussh is an overly simple wrapper script for ssh that aims to maintain
a persistent SSH tunnel. it monitors and restarts ssh sessions by using
builtin ssh features ServerAliveInterval/ServerAliveCountMax. Intended
to run with tmux sessions, it's fine for monitoring background tunnels
\`ssh -f' though. this code came up since i was looking for a solution
for reconnecting all ssh tunnels when OS's coming from a long \`ZZZ'
apm hibernation or changing network.

its usage is straightforward, just requires wrapping the ssh options.

### usage

start a ssh tunnel:

        $ aussh host

typical use, start a ssh tunnel and reattach a tmux session:
 
        $ env TTMUX=1 aussh host

 pushing ssh process to background:
 
        $ aussh -L 1050:localhost:1050 -f -N torproxy.example.org

by default, it sets a short timeout, this behaviour can be changed by
setting NO_DEFAULT_TIMEOUT and passing ServerAliveInterval/ServerAliveCountMax
either on command line or ~/.ssh/config:

        $ env NO_DEFAULT_TIMEOUT=1 aussh -oServerAliveInterval=30 \
                -oServerAliveCountMax=3  -L 10587:smtp.example.org:587 -f -N host

or pulling from ~/ssh/config:

        Host    smtp
        HostName       host
        ServerAliveInterval 30
        ServerAliveCountMax 3
        LocalForward 10587 smtp.example.com:587

        $ env NO_DEFAULT_TIMEOUT=1 aussh -f -N smtp
