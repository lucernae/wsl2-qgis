# wsl2-qgis

Run QGIS on top of WSL2 in Windows 10...
Because why not?

# Why should I do this

With the recent WSL 2 that Microsoft releases for the public. It is now possible to run Linux-based apps on top of Windows. 
By Linux-apps, I mean not only command line based-app, but GUI also. There are some benefit on why we want to use this.

1. Clean separation. Your QGIS now uses dependencies based on Ubuntu packages. No more rogue files in your Windows that will clutter your gaming computer... 
   Uh, I mean work computer.
2. Upgrading is easy. Want to switch into bleeding edge or latest stable release? Just execute `apt -y qgis` you are done. 
   No need to manually click packages to download.
3. Heaven for developers. You could run vscode in Windows, or QtCreator in Linux, to compile and build QGIS from source code... then run it on Windows.
4. Possible automations for organizations. I haven't tried it yet. But I think it's possible for an organization to create install script and 
   let their employee download and run it themselves, as long as they already setup their WSL. Which is also trivial.
5. Possible integrations with other Linux-based service. This is probably for nerds, but network interconnections is great in Linux. 
   You can develop some network based app in Linux, like GeoServer, QGIS Server, etc, which might need a web server (Apache or Nginx).
   This is easily configured in Linux compared with installing xampp or the likes in Windows and might clutter your Windows.
   You can access this service from Windows via browser as if you install the webserver itself in Windows.
   So, best of both worlds
6. Again, best of both worlds. Your programming environment in *nix based-machine is a natural habitat. 
   But you also like Windows for those productivity apps (mails, calendar, etc). 
   You want to build Windows binaries in Linux and run it straight in your Windows. Totally possible too.
   
# Why I should not do this

Well, if you are not familiar with *nix based environment and see no point of running QGIS from inside Linux, this is probably not for you.


# Requirements

You need Windows 10 2004 release at minimum
You need WSL 2 with Ubuntu distros

# Installation

1. Setup WSL 2 with Ubuntu distros

Follow the guide here:
https://docs.microsoft.com/en-us/windows/wsl/install-win10

Choose Ubuntu distros, QGIS works really good in Ubuntu.
I use Ubuntu 20.04 at the moment.

2. Install X window server in your Windows

Anything will do. I use [Xming](http://www.straightrunning.com/XmingNotes/)

3. Configure your X server

Because of WSL 2 recent network design, your windows interface and WSL interface is different, but still on the same network.
You must configure your X server to accept connection requests from different IP.
You can do so using Xming by providing extra `-ac` parameter or ticking the checkbox `No Access Control`.
By default your Display server is `0`. You can recreate your display server by exiting the current server (in your taskbar), 
and create a new one using XLaunch (Xming's tools).

4. Configure WSL to use your display server

Go into your WSL, you can do so from command prompt by executing `wsl` or search your distro name in start menu. I use Ubuntu 20.04 at the moment.
Add new line in your `~/.bashrc` file to configure the display, like this:

```shell-script
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0
```

Check the display variable from the shell

```shell-script
source ~/.bashrc
echo $DISPLAY
```

It should return something like `172.17.0.1:0`. The first part is the IP address of the bridge, the second part is the display number.
If you use different display number, like 1, then the display variable will have to be like this: `172.17.0.1:1`.

Make sure the setup is working by using telnet to check the server

```shell-script
telnet <ip address> <display number + 6000>
```

If it says, it's connected, you are good to go.

5. Install QGIS

Still from the wsl shell,
Just follow QGIS documentation for this:

https://qgis.org/en/site/forusers/alldownloads.html#debian-ubuntu

If you just want to use the LTR version, simply execute:

```shell-script
sudo apt -y update
sudo apt -y qgis
```

6. Run QGIS

Check that everything is working

```shell-script
qgis
```

If everything works, you will have a **Windows** Window pop up with QGIS in it.

To close the session, you can kill the session from the shell via CTRL+C (or just close the shell), or just close the QGIS window.

To use QGIS and shell at the same time, run QGIS as a fork.

```shell-script
qgis &
```

7. Wrapping it up for easier access

It's not convenient to open QGIS by opening the shell first. Too many steps.

Create a shortcut in your desktop by right click > New > shortcut.

In the command target field put this:

```shell-script
wsl bash -c "qgis"
```

Replace bash with other shell if you use it instead bash. For example, mine uses zsh.

In the Start In field, you could put your working directory where you put all of your QGIS projects
In the Run field, you choose Minimized to minimize the shell window (the QGIS window will still be open).

Next time you need QGIS, you can just double click the shortcut

# FAQ

1. Where is my QGIS project?

You can access your Windows drive in `/mnt` folder when you click open QGIS Project.
You can save your QGIS project there too or your WSL's home folder. Your choice.
One of the quirks

2. Why the shell window is still open when I click my QGIS shortcut?

Sorry man. I tried to fork the process too, but QGIS died after that. So this is probably a technical decision.
You can not make a separate wsl process from windows shell and let it live without the parent's Windows shell.
I also tried to minimize it, but it is still show up on the bottom taskbar.
Maybe in the future they will make a daemon-like process that can handle wsl command.
Maybe not, I don't know.

3. Can I run two instance of QGIS in parallel?

Yes you can! This is one of the benefit.
Double click the shortcut as many as you like, because it will become independent process.

