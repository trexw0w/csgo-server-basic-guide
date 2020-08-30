

Simply do the following.

1. Edit your /etc/apt/sources.list and add below line:
2. deb http://ftp.us.debian.org/debian sid main
3. apt-get update;apt install tuned tuned-gtk tuned-utils tuned-utils-systemtap
4. Complete the update and installation
5. Run the command: tuned-adm list
6. Remove or comment out the SID source in sources.list as it's unstable
