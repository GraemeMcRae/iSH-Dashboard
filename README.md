# iSH-Dashboard
For iPhone: Easy install, one-click to use, Linux server dashboard

## What is iSH?

The Linux shell for iOS. https://ish.app/

## What can you do with iSH?

I made a one-click dashboard using iSH. iSH's `.profile` has three commands: ssh to our Linux server, sleep 15, then exit. The Linux server ~/.batchrc displays the dashboard, sleeps for 10 minutes then exits, causing the ssh session to drop. Rinse and repeat.

The best part is how easy it is for non-technical managers to install iSH, install openssh, install their ssh keys, upload them to the server, and start using the app. It takes them about a minute. All they have to do is paste the following into the iSH shell, and give the server's login password when prompted:

```bash
cat > ~/.profile <<'EOF'
echo ~/.profile pass 1
sleep 10
apk update && apk add openssh nano
read -p "Enter your name: " NAME
echo "$NAME" > .myname
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "$NAME iSH" -N ""
ssh-copy-id -i ~/.ssh/id_ed25519.pub -o StrictHostKeyChecking=accept-new manager@servername.com
rm ~/.profile
scp manager@servername.com:~/iSH-setup.txt ~/.profile
source ~/.profile
EOF
source ~/.profile
```

## What is iSH-setup.txt?

This file contains the remainder of the one-time setuyp for the iSH environment on the user's iPhone, as well as the final `~/.profile` that will be used for the one-click activation of the dashboard.

Here's an example of `manager@servername:~/iSH-setup.txt`. (I could have made it way simpler, but I want to show you the one-time commands followed by the `~/.profile` bootstrap)

```bash
# The following commands are saved as ~/.profile in the iPhone user's iSH script
# You can put one-time-only setup commands here, such as `apk add <software package>` etc.
echo ~/.profile pass 2
sleep 10
cat > ~/.profile <<'EOF'
ssh manager@servername.com
sleep 15
exit
EOF
source .profile
```

## How is the dashboard invoked?

At the bottom of the server's `~/.bashrc` we have the following:

```bash
if [[ $- == *i* ]]; then
    ( ~/dashboard.sh )
    sleep 600
    exit
fi
```

## What other files do I need to make it work?

There's just one other moving part not already shown, above. It's the dashboard itself. It's a shell script called `~/dashboard.sh`. It can be anything you want your managers to check, say, at the beginning and end of their shift. In our case, we look for messages in the log files of a Discord bot and a cron job.
