

## Before You Begin

## Install SteamCMD

SteamCMD can be installed via your distribution's [package manager] or through a [manual method].

### From Package Repositories (Recommended)

Installing via the package manager allows you to more easily download updates and security patches, so we strongly recommend using this method if your distribution includes the SteamCMD package. The package is available for Ubuntu and Debian deployments.

-   **Ubuntu**

    1.  Install the package:

            sudo apt-get install steamcmd

    1.  Create a symlink to the `steamcmd` executable in a convenient place, such as your home directory:

            cd ~
            ln -s /usr/games/steamcmd steamcmd

-   **Debian**

    1.  Add the `non-free` area to the repositories in your sources list, because the `steamcmd` package is only available from this area. To do so, edit your `/etc/apt/sources.list` file and include `non-free` at the end of each `deb` and `deb-src` line, as in this snippet:

### file "/etc/apt/sources.list"
```
deb http://mirrors.linode.com/debian stretch main non-free
deb-src http://mirrors.linode.com/debian stretch main non-free
...
```

    1.  Add the i386 architecture, update your package list, and install `steamcmd`:

            sudo dpkg --add-architecture i386
            sudo apt update
            sudo apt-get install steamcmd

    1.  Create a symlink to the `steamcmd` executable in a convenient place, such as your home directory:

            cd ~
            ln -s /usr/games/steamcmd steamcmd

### Install Manually

If your package manager does not include the `steamcmd` package, install it manually:

1.  Newly created Linodes use 64-bit Linux operating systems. Since Steam is compiled for i386, install the appropriate libraries. For CentOS, also install `wget`.

    -   **CentOS 7, Fedora**

            sudo yum install glibc.i686 libstdc++.i686 wget

    -   **Debian, Ubuntu**

            sudo apt-get install lib32gcc1

    {{< note >}}
Running `dpkg --add-architecture i386` is not necessary at this point. Our Steam game guides add [multiarch support](https://wiki.debian.org/Multiarch/HOWTO) only when a game requires it.
{{< /note >}}

1.  Create the directory for SteamCMD and change to it:

        mkdir ~/Steam && cd ~/Steam

1.  Download the SteamCMD tarball:

        wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz

1.  Extract the installation and runtime files:

        tar -xvzf steamcmd_linux.tar.gz

{{< note >}}
When running a Steam game, you may encounter the following error:

    /home/steam/.steam/sdk32/libsteam.so: cannot open shared object file: No such file or directory

The game server will still operate despite this error, and it should be something fixed in a later release of SteamCMD. The temporary fix is to create the directory and symlink to `libsteam.so`.

    mkdir -p ~/.steam/sdk32/
    ln -s ~/Steam/linux32/steamclient.so ~/.steam/sdk32/steamclient.so

{{< /note >}}

## Run SteamCMD

1.  Run the executable in a screen session:

    If you have installed SteamCMD from repositories:

        screen ~/.steam/steamcmd

    If you have installed SteamCMD manually:

        screen ~/Steam/steamcmd.sh

    That will return an output similar to below and leave you at the `Steam>` prompt:

        Redirecting stderr to '/home/steam/Steam/logs/stderr.txt'
        [  0%] Checking for available updates...
        [----] Downloading update (0 of 7,013 KB)...
        [  0%] Downloading update (1,300 of 7,013 KB)...
        [ 18%] Downloading update (3,412 of 7,013 KB)...
        [ 48%] Downloading update (5,131 of 7,013 KB)...
        [ 73%] Downloading update (6,397 of 7,013 KB)...
        [ 91%] Downloading update (7,013 of 7,013 KB)...
        [100%] Download complete.
        [----] Installing update...
        [----] Extracting package...
                . . .
        [----] Cleaning up...
        [----] Update complete, launching Steam...
        Redirecting stderr to '/home/steam/Steam/logs/stderr.txt'
        [  0%] Checking for available updates...
        [----] Verifying installation...
        Steam Console Client (c) Valve Corporation
        -- type 'quit' to exit --
        Loading Steam API...OK.

        Steam>

1.  Most Steam game servers allow anonymous logins. You can verify this for your title with Valve's list of [dedicated Linux servers](https://developer.valvesoftware.com/wiki/Dedicated_Servers_List#Linux_Dedicated_Servers).

    To log in anonymously:

        login anonymous

    To log in with your Steam username:

        login example_user

    {{< caution >}}
Some versions of the Steam CLI do **not** obfuscate passwords. If you're signing in with your Steam account, be aware of your local screen's security.
{{< /caution >}}

## Exit SteamCMD

### Detach from the Screen Session

To exit the screen session which contains the Steam process *without* disrupting the Steam process, enter **Control+A** followed by **Control+D** on your keyboard. You can later return to the screen session by entering:

    screen -r

For more information on managing your screen sessions, review our [Using GNU Screen to Manage Persistent Terminal Sessions](/docs/networking/ssh/using-gnu-screen-to-manage-persistent-terminal-sessions/) guide.

### Stop SteamCMD

To stop the Steam process and remove your screen session, enter `quit` at the `Steam>` command prompt, or enter **Control+C** on your keyboard.


{{< note >}}
This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you’re not familiar with the `sudo` command, you can check our [Users and Groups](/docs/tools-reference/linux-users-and-groups) guide.
{{< /note >}}

## Prerequisites for Counter-Strike: Global Offensive

From the SteamCMD guide, one additional step is needed specifically for CS:GO.

1.  Replace a firewall rule to slightly extend the port range available to the game. This command assumes that you have **only** the iptables rules in place from the SteamCMD guide:

        sudo iptables -R INPUT 5 -p udp -m udp --sport 26900:27030 --dport 1025:65355 -j ACCEPT

## Install Counter Strike: Global Offense

1.  Be sure you are in the directory `~/Steam`, then access the `Steam>` prompt.

        cd ~/Steam && ./steamcmd.sh

2.  From the SteamCMD prompt, login anonymously:

        login anonymous

    Or log in with your Steam username:

        login example_user

3.  Install CS:GO to the `Steam` user's home directory:

        force_install_dir ./csgo-ds
        app_update 740 validate

    This can take some time. If the download looks as if it has frozen, be patient. Once the download is complete, you should see this output:

        Success! App '740' fully installed.

        Steam>

4.  Exit SteamCMD.

        quit

    {{< note >}}
To update CS:GO, run the above 4 commands again.
{{< /note >}}

## Game Server Login Token

CS:GO requires a server token unless you want to limit players to only clients connecting from within the server's LAN. This requires having a Steam account and owning CS:GO. See [Valve's CS:GO wiki](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive_Dedicated_Servers#Registering_Game_Server_Login_Token) for more info on the GSLT.

## Configure the Server

1.  Create a file called `server.cfg` using your preferred text editor. Choose a hostname and a unique RCON password that you don't use elsewhere.

### ~/Steam/csgo-ds/csgo/cfg/server.cfg" 
```
hostname "server_hostname"
sv_password "server_password"
sv_timeout 60
rcon_password "rcon_password"
mp_autoteambalance 1
mp_limitteams 1
writeid
writeip
```


    For an extensive list of `server.cfg` options, see [this page](http://csgodev.com/csgodev-server-cfg-for-csgo/).

2.  Create a startup script for CS:GO with the contents given below. **Be sure to replace `YOUR_GSLT` in the script's command with your game server login token**.

###  "~/startcsgo.sh"
```
#!/bin/sh

cd ./Steam/csgo-ds
screen -S "Counter-Strike: Global Offensive Server" ./srcds_run -game csgo -usercon +game_type 0 +game_mode 1 +mapgroup mg_bomb +map de_dust2 +sv_setsteamaccount YOUR_GSLT -net_port_try 1
```



    When run, the script will change directories to `~/Steam/csgo-ds` and execute a Dust2 server in competitive game mode in a [Screen](/docs/networking/ssh/using-gnu-screen-to-manage-persistent-terminal-sessions) session. For more startup modes and game options, see Valve's [CS:GO wiki](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive_Dedicated_Servers#Starting_the_Server).

3.  Make the script executable:

        chmod +x ~/startcsgo.sh

## Start the Server

1.  Now that your server is installed and configured, it can be launched by running the `startcsgo.sh` script from your `steam` user's home directory.

        cd ~/ && ./startcsgo.sh

    {{< caution >}}
From this point, do not press the **Control+C** keys while in the console unless you want to stop CS:GO.
{{< /caution >}}

2.  To detach from the screen session running the server console, press these two key combinations in succession:

    **Control+A**<br>
    **Control+D**

3.  To bring the console back, type the following command:

        screen -r

4.  To stop the server, bring back the CS:GO console and press **CONTROL + C**.

## Join the Game

1.  Launch Counter-Strike: Global Offensive.

2.  Once launched, go to **Play** and click **Browse Community Servers**.

3.  Click on the **Favorites** tab and then click **Add a Server** at the bottom.

4.  Type in the IP address of your Linode and click **Add this address to favorites**.

5.  You'll see your new Counter-Strike: Global Offensive server. Click **Connect** at the bottom right and start fragging away.


## Game Settings

### Game Modes and Types

You can change the game type and mode options to start different types of servers:

    Mode                   game_mode    game_type
    Classic Casual             0            0
    Classic Competitive        0            1
    Arms Race                  1            0
    Demolition                 1            1

These settings are changed in the launch command.

### RCON

When logged into the server, you can open the RCON console with the backtick button (`` ` ``), or your mapped key. To log in type `rcon_password` followed by your password. For more information regarding RCON, click [here](/docs/game-servers/team-fortress2-on-debian-and-ubuntu/#rcon).
