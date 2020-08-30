This tutorial has been tested on hardware (dedicated server):
[LIST]
[*]Intel Core i7-3770 (3.40 GHz / 3.90 GHz)
[*]HDD2x HDD SATA 3,0 TB
[*]RAM2x RAM 8192 MB DDR3
[/LIST]
OS:
[LIST]
[*]Debian GNU/Linux 9 (stretch)
[*]SMP Debian 4.9.189-3+deb9u2 (2019-11-11) x86_64
[/LIST]
Open your server console and follow the instructions.
[LIST=1]
[*] Install important compilation packages.
[PHP]
apt install fakeroot ca-certificates build-essential bison flex gnupg libncurses-dev libelf-dev libssl-dev wget bc rsync
[/PHP]
[*] 
[PHP]
gpg --locate-keys torvalds@kernel.org gregkh@kernel.org
[/PHP]
[*]
[PHP]
mkdir ~/kernel
cd ~/kernel
[/PHP]
[*]Download the latest kernel from: [URL="https://www.kernel.org/"]https://www.kernel.org/[/URL] (yellow button or stable -> tarball)
[PHP]
wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.7.tar.xz
wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.7.tar.sign
[/PHP]
[*] Unpack the package. If package verification fails, remove (| gpg --verify linux-5.4.7.tar.sign -).
[PHP]
unxz -c linux-5.4.7.tar.xz | gpg --verify linux-5.4.7.tar.sign -
cd linux-5.4.7
[/PHP]
[*] We're going to set up your kernel.
[PHP]
make menuconfig
[/PHP]
[*]Now set according to the settings tree.
[PHP]
Processor type and features

    [*] CPU Frequency scaling
     Timer frequency (xxx Hz) ---> 1 000 Hz

-------------------------------------------------------------------
Power management and ACPI options -> CPU Frequency scaling
    Default CPUFreq governor (performance) --->

    [*] CPU Frequency scaling

    [*]   'performance' governor
    []   'powersave' governor
    []   'userspace' governor for userspace frequency scaling
    []   'ondemand' cpufreq policy governor
    []   'conservative' cpufreq governor
    []   'schedutil' cpufreq governor
[/PHP]
[*]Save everything and exit.
[*]Check the following settings in .config and save.
[PHP]
nano .config
CONFIG_SYSTEM_TRUSTED_KEYS = ""
CONFIG_DEBUG_INFO=n
[/PHP]
[*]You can use your own name instead of [B]fastkernel[/B].
[PHP]
make clean
make deb-pkg LOCALVERSION=-fastkernel
[/PHP]
[*]The kernel is compiling. Hard work. Here you have some bacon as a reward. :bacon!: (Now you have about 2 hours of free time ~ depends on processor performance). :wink:
[*]We have a compiled kernel. Install the kernel. 
[PHP]
Install
=====
dpkg -i ../linux-image-5.4.7-fastkernel_5.4.7-fastkernel-1_amd64.deb

Uninstall
=====
apt-get purge fastkernel_5.4.7
[/PHP]
[*]Restart the server and enter the command after booting.
[PHP]
uname -r
--> You will get: 5.4.7-fastkernel <--
[/PHP]
[*]We turn on the Intel turbo.
[PHP]
echo 0 > /sys/devices/system/cpu/intel_pstate/no_turbo
[/PHP]
[*]Now let's set the CPU's permanent performance to maximum. Create a [B]cpu.sh[/B] file somewhere. [B]Modify the settings according to your processor![/B]
[PHP]
apt install cpufrequtils //Install this tool, otherwise it will not work.
[/PHP]
[PHP]
#!/bin/bash

cpufreq-set --cpu 0 --governor performance
cpufreq-set --cpu 1 --governor performance
cpufreq-set --cpu 2 --governor performance
cpufreq-set --cpu 3 --governor performance
cpufreq-set --cpu 4 --governor performance
cpufreq-set --cpu 5 --governor performance
cpufreq-set --cpu 6 --governor performance
cpufreq-set --cpu 7 --governor performance

cpufreq-set -u 3.90GHz
cpufreq-set -d 3.90GHz

cpufreq-set -r -g performance
[/PHP]
[*]Now let's set the script to run every time the server restarts. I put the startup script into [B]etc/rc.local[/B] (the file does not normally exist). We'll create it.
[PHP]
nano /etc/systemd/system/rc-local.service
[/PHP]
[PHP]
[Unit]
Description=/etc/rc.local
ConditionPathExists=/etc/rc.local

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
StandardOutput=tty
RemainAfterExit=yes
SysVStartPriority=99

[Install]
WantedBy=multi-user.target
[/PHP]
[PHP]
nano /etc/rc.local
[/PHP]
[PHP]
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
/cpu.sh || exit 1

exit 0
[/PHP]
[PHP]
chmod +x /etc/rc.local
systemctl enable rc-local
systemctl start rc-local.service
systemctl status rc-local.service
[/PHP]
[*]Restart the server.
[*]Type the command [B]cpupower frequency-info[/B]. You should see something like that.
[PHP]
analyzing CPU 0:
  driver: intel_pstate
  CPUs which run at the same hardware frequency: 0
  CPUs which need to have their frequency coordinated by software: 0
  maximum transition latency:  Cannot determine or is not supported.
  hardware limits: 1.60 GHz - 3.90 GHz
  available cpufreq governors: performance powersave
  current policy: frequency should be within 3.90 GHz and 3.90 GHz.
                  The governor "performance" may decide which speed to use
                  within this range.
  current CPU frequency: Unable to call hardware
  current CPU frequency: 3.40 GHz (asserted by call to kernel)
  boost state support:
    Supported: yes
    Active: yes
    3700 MHz max turbo 4 active cores
    3800 MHz max turbo 3 active cores
    3900 MHz max turbo 2 active cores
    3900 MHz max turbo 1 active cores
[/PHP]
[/LIST]
