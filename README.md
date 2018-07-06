# aussh

aussh is an overly simple wrapper script for ssh that aims to maintain
a persistent SSH tunnel. it monitors and restarts ssh sessions by using
builtin ssh features ServerAliveInterval/ServerAliveCountMax. Intended
to run with tmux sessions, it's fine for monitoring background tunnels
\`ssh -f' though. this code came up since i was looking for a solution
for reconnecting all ssh tunnels when resuming from \`ZZZ(8)'
apm hibernation or changing network.

It uses ssh(1)/ServerAliveInterval to send a message through the encrypted
channel to request a response from the server, and ssh(1)/ServerAliveCountMax
to set the number of server alive messages which may be sent without ssh(1)
receiving any messages back from the server. see ssh_config(5) manpage for
futher details.

For background tunnel -n flag must be used, it prevents reading from
stdin and ssh(1) does not work if it needs to ask for a password or
passphase, so for using `ssh -f' is required to use public keys
autehtication, see ssh-agent(1).

Its usage is straighforward, just requires wrapping ssh(1) options.

### usage

        # start a ssh tunnel
        $ aussh host -oServerAliveInterval=5 -oServerAliveCountMax=2

        # pushing ssh process to background
        $ aussh  -oServerAliveInterval=5 -oServerAliveCountMax=2  \
                -n -fNL 10587:smtp.example.org:587 host

	# typical use, start a ssh tunnel and reattach a tmux session
        # using ~/.ssh/config for pulling options:

        Host irc
        HostName host.org
        ServerAliveInterval 5
        ServerAliveCountMax 2
        RequestTTY yes
        RemoteCommand tmux attach-session -t irssi || tmux new-session -s irssi

        Finally, $ aussh irc
