---
tags: wsl
---

# Everything I Know WRT Enabling and Hacking WSL for Windows 10

> Sidebar: On 2021-03-12, I took an Azure Certification Exam by Pearson and it was **very much not allowed** to have a Hyper-V running. In order to meet this pre-requisite, it was disabled. After the test (passed, btw) Docker broke and so did WSL. It took two months to figure out which toggle closed-out by the Pearson tech and undo the blocker issue:  "...could not be started because a required feature is not installed." 
😒


## The Toggle
1. Open `services.msc`
2. Locate the 'Hyper-V Host Compute Service'; Description: Provides support for running Windows Containers 
3. Enable it to start 'Automatic' 


## Prep or Re-dos
```
# where are you at?
wsl --list --verbose
# need to get started? 👇 (then reboot)
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
# Then install the [Linux kernel package](https://aka.ms/wsl2kernel)

wsl --set-default-version 2
# Then install Ubuntu 20.04
```


## Hacking for Support of `systemd`
* Via Sam Ruby's blog post: [intertwingly: Prepping a Windows Machine for Development](https://intertwingly.net/blog/2020/07/26/Prepping-a-Windows-Machine-for-Development), you can enable added Linux-y\developer-y features, but your going to have to hack that Ubuntu20.04 install. Lets work with his sequence of tasks:
1. PS > `ubuntu2004.exe config --default-user root`
2. PS > `wsl`
3. root@ubuntu: `apt-get update && apt-get install -y dbus policykit-1 daemonize`
4. root@ubuntu: `touch /usr/bin/bash-bootstrap-services`
5. root@ubuntu: `chmod +x /usr/bin/bash-bootstrap-services`
6. root@ubuntu: `editor /usr/bin/bash-bootstrap-services`
7. Paste in the shell code in step 2 of the repo.
8. Replace the `<YOURUSER>` with the WSL "owner" that you selected at install of Ubuntu20.04.
9. Edit `/usr/sbin/daemonize` to ` /usr/bin/daemonize` in the copied-over shell code lest it will not work!
10. Save file. Execute it. You should get logged in as the default WSL "owner".
11. YOURUSER@ubuntu: `exit` # success! right?
12. root@ubuntu: `editor /etc/passwd`
13. Modify line 1 to: `root:x:0:0:root:/root:/usr/bin/bash-bootstrap-services`
14. Save and Close file. Exit session.
15. PS > `wsl --shutdown`
16. PS > `wsl`
17. YOURUSER@ubuntu: `systemctl is-active dbus`
↩ active 
18. YOURUSER@ubuntu: ...created an `/etc/rc.local` and enjoys vast success....


## Resources
[Microsoft docs: WSL Troubleshooting](https://docs.microsoft.com/en-us/windows/wsl/troubleshooting)
[Microsoft docs: WSL CLI Reference](https://docs.microsoft.com/en-us/windows/wsl/reference)
[Sam Ruby's fork of the 'wsl2-hacks' repo](https://github.com/rubys/wsl2-hacks)
[Awesome WSL](https://github.com/sirredbeard/Awesome-WSL)


#### My version of bash-bootstrap-service
```
#!/bin/bash
# your WSL2 username
UNAME="skleuth"

UUID=$(id -u "${UNAME}")
UGID=$(id -g "${UNAME}")
UHOME=$(getent passwd "${UNAME}" | cut -d: -f6)
USHELL=$(getent passwd "${UNAME}" | cut -d: -f7)

if [[ -p /dev/stdin || "${BASH_ARGC}" > 0 && "${BASH_ARGV[1]}" != "-c" ]]; then
    USHELL=/bin/bash
fi

if [[ "${PWD}" = "/root" ]]; then
    cd "${UHOME}"
fi

# get pid of systemd
SYSTEMD_PID=$(pgrep -xo systemd)

# if we're already in the systemd environment
if [[ "${SYSTEMD_PID}" -eq "1" ]]; then
    exec "${USHELL}" "$@"
fi

# start systemd if not started
/usr/bin/daemonize -l "${HOME}/.systemd.lock" /usr/bin/unshare -fp --mount-proc /lib/systemd/systemd --system-unit=basic.target 2>/dev/null
# wait for systemd to start
while [[ "${SYSTEMD_PID}" = "" ]]; do
    sleep 0.05
    SYSTEMD_PID=$(pgrep -xo systemd)
done

# enter systemd namespace
exec /usr/bin/nsenter -t "${SYSTEMD_PID}" -m -p --wd="${PWD}" /sbin/runuser -s "${USHELL}" "${UNAME}" -- "${@}"
```