# Setup WSL2 with care

Hello, Thank you for attending this talk. Today I'm speaker at the DevOpsDays Geneva 2022.<br/>
My name in [Olivier Crozier](https://www.linkedin.com/in/oliviercrozier/). I'm engineer at Kudelski Group. And I'll present you how to `Setup WSL2 with care`.

# In other words

[Turn your Windows 10 as a great Linux dev platform with WSL2](https://devopsdays.org/events/2022-geneva/program/olivier-crozier).

# We have 1,5 hour together

With half presentation, and half questions, discussions, demo.
The presentation will be divided in 3 parts:

1. [Installation and configuration](#lets-start)
2. [Organize your environments](#organize-your-environments)
3. [Advanced uses](#advanced-choose-your-container-runtime)

# Let's clarify who I am

I am not affiliated with Microsoft,
I am not selling anything,
I am not a speaker or technical writer (at least on Internet).

- I am used to share knowledge within my company<br/>
- I am used to give presentations, training, and guidance about the tools and processes that I'm managing with my team<br/>
- Eventually, I am a developer who needs Linux on my Windows

# Today I'm working at

The Kudelski Group is a [technology pioneer](https://vimeo.com/305051552) since 1951 with the Nagra Audio.<br/>
The [Kudelski Group](https://www.nagra.com/) has activities in Multiscreen and Digital TV*, Public Access, Cybersecurity, and Internet of Things.<br/>
\*This is where I'm working :-).

# Why talking about WSL2 ?

1. First, why having Linux, and a Linux VM on Windows ?<br/>
   &rarr; Because most of the backends run on a Linux infrastructure, and more and more if talking about virtualization and containers. On Windows too, we need to be as close as the target environment for our local developments.
2. So, why having WSL then ?<br/>
   &rarr; To provide a better experience to the developers on Windows.
3. Then, why WSL2 ?<br/>
   &rarr; Simply because there's no way to install any Linux software like docker on WSL1. On WSL1, we need to rely on a Windows app `Docker for Desktop` to get something than runs natively on Linux.

# In a nutshell

WSL2 provides a `Full Linux` plus a `seamless integration` with Windows.

- With WSL2, we can install any software like `docker` or `kubernetes`.
- We can share files in both directions. We can even set Linux permissions to the Windows files.
- We can run whichever executable on whichever system.
- We have that cool feature `localhost forwarding`, which means that:<br/>
  Any app which you serve in WSL2 on a port on localhost, is also accessible at localhost same port on Windows side too.
- WSL2 has a better use of the hardware resources.
- The only drawback compared to WSL1 is its bad performance across the OS filesystems.

# You should manage your laptop

...like any cloud resource.
You are coming from Windows; you know what services slow your computer down.
That could be a Windows update, the antivirus, the search indexer, or even your browser, or Teams.
But that could be a Linux app which you serve in docker or kubernetes, and that makes a heavy use of the resources, like the disk.
Usually, the `Windows Task Manager` is your friend, to understand what's happen, so you can fix.

# Let's start

...with WSL commands. The first thing you'll need to do is upgrade WSL to version 2.
Then you can list all distributions available online or installed locally.
So, you can install one distribution, start it, stop it, or stop all local distributions at once, and uninstall one local distribution.
There are several other commands.

See the official [documentation](https://docs.microsoft.com/en-us/windows/wsl) and [github](https://github.com/microsoft/WSL) project.

# Choose your distribution

In addition to the distributions listed with previous commands, you can go to `Microsoft Store` and search for any distribution that matches your system.
One of them is `Pengwin`. This is the first Linux distribution optimized for WSL. It is chargeable but it comes with an installer which helps you to configure your shell, install initial packages, etc.

# Install WSL2

I prefer to install a standard distribution, so I'll go with Ubuntu.
Install WSL2 is pretty easy. There are few prerequisites. You need a recent Windows, have the WSL2 Linux kernel installed, and the Virtualization feature enabled. I tested with Hyper-V, it is said to be working with VirtualBox and VMWare too.

To install WSL2, you only need to upgrade WSL to version 2, and install the distribution you want.
You will probably need to reboot your computer, and complete the installation, with a default username.

# Start WSL2 - Which terminal ?

`Ubuntu` comes with its terminal, which is nice, but limited, because you can use it only with the default Ubuntu, not with all locally installed distributions.
Alternatively, from a `PowerShell` or Windows command prompt, you can run the command 'wsl', without parameters it launches the default distribution.
I tried from `GitBash`, but 'wsl' command fails in MinTTY. As a workaround, after starting the sshd service on WSL2, I could ssh to WSL2, using its allocated IP.
Still, this 2 steps operation (open terminal, then enter WSL2) is not optimal. So, I tried `Windows Terminal`, which is an acceptable solution, because I can configure it to start WSL2 when I open it. And I can define other profiles to start PowerShell or GitBash in other tabs.

Tip: Any new terminal has its own shortcuts, like copy/paste with the mouse, or scroll up with the keyboard.

# Configure WSL2

There are 2 levels of [configuration](https://docs.microsoft.com/en-us/windows/wsl/wsl-config).
1. One global configuration where to limit the resources that WSL2 can use, and enable the `local forwarding` feature.
2. One specific configuration per distribution.

> In the `network` part, you can personalize the hostname (very useful for the shell prompt). The generateHosts can be useful if you define several DNS aliases in your Windows hosts file, so it is nice to have them in WSL too. However, letting Windows to generateResolvConf is probably not a good idea.

> In the `interopt` part, it is interesting to enable the feature so you can run Windows executables from WSL. However, I am not in favor to appendWindowsPath because I think we should separate the environments. If you are on Windows, you have Windows paths. If you are on Linux, you have Linux paths. If you want to run a Windows executable from WSL, either you write the full path to the file, or you define an alias, but at least you manage your environments.

# First install

Your initial WSL is quite empty.
- As you'll run many sudo commands, you may want to remove password.
- Then you should re-generate the /etc/resolv.conf, especially if you configured WSL without generateResolvConf. Initially, /etc/resolv.conf is a symlink to a file in /run that Windows creates on WSL startup (if configured), that links to a file that does not exist (if not configured). After removing the symlink, you should create a new file with an initial content like 'nameserver 1.1.1.1', at least to get the DNS resolution for the public aliases.
- Then you should update cache and upgrade packages, before you can install new ones.
- You may configure your shell, here bash, it could zsh or whatever.
- You may configure a proper title for your terminal, and prompt for your shell.
- Maybe arrange other settings, like the editor colors that may render differently depending on which terminal you use.

# Reboot ?

`sudo reboot` does nothing because **there is no systemd** on WSL2.

Only Windows manages the WSL state, and you should use the `wsl` commands to start and stop the machine.

# Start services

You should use the default command `service`: 'sudo service ${name-of-the-service} start' or stop/status.

If you want to use systemd anyway, you can install one of the available solutions. You will be able to manage the services, however 'sudo reboot' will still do nothing, and if you want to install a software that relies on systemd, it will probably fail.

The question is: [Should systemd be used](https://wsl.dev/wsl2init/), knowing that not all the Linux distributions use it?

# Connect Pageant for ssh

Let's ssh to a remote machine, either using username/password, or better using a ssh key pair (ex: if 'git clone' using ssh). You configure the public key on the remote, and you authenticate locally using the private key. Now, for security, it is better to define a passphrase to your ssh private key. However, having to write the passphrase to every ssh command is very annoying. The solution is to start a ssh agent, and load the private key in it, when only to enter the passphrase. You can use any private key loaded into your ssh agent to your further ssh commands.

You can start a ssh agent on both WSL and Windows sides, but **why not having one ssh agent for all ?**

There is an interesting [question on stackoverflow](https://stackoverflow.com/questions/62937020) with this interesting picture, which says that:
- There are several possible ssh agents, like Windows Pageant.
- On client side, either the client can fetch the ssh keys directly from the ssh agent, or via a proxy.

With Windows Pageant, when you start it, you have an icon in your Windows notification bar. When double-click on it, you can add key, enter passphrase, and make it available.<br/>
&rarr; Many native Windows apps are able to fetch the ssh keys from Windows Pageant, like FileZilla or Tortoise Git.<br/>
&rarr; From GitBash, you need to start a proxy which connects you to Windows Pageant.<br/>
&rarr; From PowerShell*, the solution is to create the environment variable `SSH_AUTH_SOCK` for which the value is the output of the command: `(Get-ChildItem \\.\pipe\).FullName | findstr pageant`.

You can always list the keys loaded into your ssh agent with: `ssh-add -l`.

\*Tip: Few Windows apps like `vscode` use Windows ssh only.

# Connect Pageant for ssh in WSL2

Similar solution to same problem: You can use the proxy [wsl2-ssh-pageant](https://github.com/BlackReloaded/wsl2-ssh-pageant).

Well, it did not function straightly for me, I had to implement 2 workarounds:
1. With the solution listed in this comment [issues/23#issuecomment-882068132](https://github.com/BlackReloaded/wsl2-ssh-pageant/issues/23#issuecomment-882068132), see the full code in the slide.
2. The last version of the executable was blocked by my company antivirus, so I had to use the earlier version.

# Work through a VPN

There are 2 sub-questions:
1. How to keep access to local network resources while connected through VPN ? This is a configuration to be done on your VPN client.
2. How to get DNS resolution for both local and remote resources ? You'll have to play with your /etc/resolv.conf<br/>
   PS: Normally the local gateway (the Windows host IP through which WSL gets its network) should resolve all DNS, however it may fail in some situations, especially from a container within WSL2.

# Setup a fixed IP

What you should know:

At every Windows reboot, your WSL2 is assigned to a different IP always, and not always within same IP range, it could be 192.168.y.z or 172.x.y.z.

You may need a fixed IP, like in this example, or if you serve a Kubernetes app from WSL2, listening to myapp.192.168.50.2.nip.io for ex.

# The boot process

There is an opened [issue on github](https://github.com/microsoft/WSL/issues/4210) to track this request to be able to configure a static IP to WSL2.

There are solutions to workaround this problem, and few tools to implement them, like [mine](https://github.com/ocroz/wsl2-boot) (because I did not like the other ones). Note that I'm not selling it and I don't consider it as a professional product.

In brief, `wsl2-boot` does the following at every Windows reboot:
- It makes sure that all WSL network resources are deleted cleanly,
- It creates the WSL network, and connect the Hyper-V VMs to it,
- It fetches the DNS information from the current connected network, and starts WSL with this information,
- It assigns a fixed IP to WSL, patches the DNS information, and optionally starts few services, like crond which can trigger other actions in background.

And `wsl2-boot` re-starts every time Windows connects or disconnects a network, to patch the DNS information on the fly.

# Organize your environments

See the 3 environments you have: The Windows part, the WSL part, and the containers within WSL.

1. Windows is the hardware which you received, with the screen where to display both the Windows apps (source editor, IDE) and Linux graphical apps (via a X server on Windows), where you receive the network, configure the ssh keys, and start the terminal to enter WSL.
2. WSL is where you have your source files, your shell environment, where you run the container engine and kubernetes, and install few tools to administrate your WSL.
3. The containers is for everything specific, with the project's environment variables and binary files, the library and dependency files, where you build, test, deploy, whatever app, even graphical.

# Why save files on WSL2 ? Because it is faster

Let's consider you configured the shell prompt with a git context, and let's enter a git repository is various situations:

1. First, using GitBash, on a Windows file system, press enter, and you'll get the new prompt after 1 second.
2. Second, using WSL2, on same Windows file system, press enter, and you'll get the new prompt after 0.5 second.
3. Third, using WSL2, on WSL2 file system, press enter, and you'll get the new prompt quite immediately.<br/>
   Of course, save the files on Windows side may be longer, and WSL2 must be up or you don't even access them.

# Linux ownership and permissions to Windows file

By default:
- The Windows files are showed as owned by the `default user` which you configured in WSL, which is root if none configured.
- The Windows files are opened to everyone (0777).

You can define `umask` and `fmask` options to configure default permissions to dirs (0755) and files (0744) on Windows files.
With `metadata` you can set more precise permissions on Windows files thanks to the Linux command `chmod`.

# Line endings in a mix environment

Another problem you'll have is the difference with the line endings on Windows and Linux sides.
You should fix and decide a default, knowing that Windows is able to manage Linux files, but Linux has more problems with Windows files.

Let's configure Linux line endings everywhere:

1. First in your source editor.
2. Secondly, in your git repositories, on both Windows and Linux sides. If you have existing repos, you may need to enter them all, and update their files in cache. Remember, the git context in the shell prompt can help you greatly.

# VSCode : Remote WSL, Container, SSH

Let's talk about source editors, especially VSCode, which comes with an interesting plugin [Remote Development](https://code.visualstudio.com/docs/remote), which comes with 3 sub-plugins: Remote WSL, Remote Container, and Remote SSH.

VSCode can connect to a remote host, here using Remote SSH on machine 'bill-ubuntu18' (see the green box in the bottom left corner). With VSCode, you are using a Windows app connected to a remote host where the files are located and where the commands you run in VSCode terminal are executed.

When connected to a remote WSL or SSH, you can run a Remote Container using `docker` or `podman` which is installed on the remote host.

PS: Remote SSH uses the Windows ssh command, which you can connect to Windows Pageant too (see previous slides).

# Docker : The Linux way

Good news: You don't need `Docker for Desktop` anymore :-).

You have a Linux machine. Let's install docker the Linux way. For Ubuntu, let's follow the [official guide](https://docs.docker.com/engine/install/ubuntu/). Once installed, just start the service, and you are done. See, both the client and the server are running on a Linux architecture, your WSL2.

Hum... What if you use an IDE like `Eclipse` with a docker plugin that needs docker on Windows?<br/>
&rarr; No problem, just download [docker.exe](https://github.com/StefanScherer/docker-cli-builder/releases) for docker command line on Windows, and connect it to docker engine on WSL2:
- `localhost forwarding` must be enabled.
- You should reconfigure the docker engine to listen to IP 0.0.0.0, and restart the service on WSL2.
- On Windows, you should create an environment variable `DOCKER_HOST` for which the value in `localhost:2375`.

See now, the client is running on Windows architecture, and the server is running on a Linux architecture, your WSL2.

# Docker / Podman : Test our installation

Let's start a docker or podman container on WSL2, and share the ssh socker with the container, so from within the container, you have access to the ssh keys which you loaded into Windows Pageant.

Then, let's take the example of `ansible` which fails in various situations:
- `ansible ping` will fail if network problems or no ssh keys loaded.
- `yamllint` will fail if the files are saved with the Windows line endings.
- `ansible-playbook` will fail if the file permissions are opened to everyone.

# Kubernetes : Within docker

Now that you have docker on Linux, let's install `kubernetes` within docker, here with the example of [k3d](https://github.com/rancher/k3d#get), very easy to install.
There is a [nice article talking about k3d](https://en.sokube.ch/post/k3s-k3d-k8s-a-new-perfect-match-for-dev-and-test-1). This article says nothing about WSL2, but you have a true Linux machine, so it applies to you as well.

Another word, when you start a kubernetes cluster, it opens few ports like 8080 and 8443. For debug purpose, you may need to open an `insecure kubernetes dashboard` thanks to the command `kubectl proxy &` which opens another port 8001 at localhost. Well, thanks to `localhost forwarding`, you can access the kubernetes dashboard at `localhost:8001` from a browser on Windows side, and where you can authenticate too :-).

# Remote display

If you need to run Linux graphical apps, you need to install and start a X server on Windows where they will display.

The DISPLAY address refers to the local gateway (the Windows host IP through which WSL gets its network).

This could be useful for browser testing/automation using `selenium` for example.

# Shift-left CI/CD

You all know the DevOps loop where we code, build, test, release, deploy, operate, monitor, then plan for new code changes.

For more efficiency, people implement a Centralized CI/CD. To be even more efficient, it is wise to run the Centralized CI/CD locally too. Some people go even futher, and as soon as they saved their files, the Local CI/CD starts immediately.

For those who are using GitLab CI/CD, you can look at [gitlab-ci-local](https://github.com/firecow/gitlab-ci-local) to run the Centralized CI/CD locally without effort.

# Advanced: Choose your container runtime

Here I took the picture from the other presentation ["It works on my browser", Shift your Workspaces to the Cloud](https://www.linkedin.com/posts/sestegra_d%C3%A9placez-vos-espaces-de-travail-sous-kubernetes-activity-6877919030217347072-_SEK/).

When you run `docker version`, you see that docker has several components: a runtime driven by the standard OCI specs API, containerd, and docker itself for the command line interface.

Nothing prevents you to change one of the components, without changing docker itself. You can switch to a different runtime, which is `runc` by default, to the other runtime `sysbox`. See next slide.

# Sysbox : System containers

Provided by `nestybox`, `sysbox` empowers containers to run software like `systemd`: You have a complete systemd in your container, so you can install `docker`, `kubernetes`, and any `legacy app`, `seamlessly` (no need to learn a different langage, deploy your container with same docker commands) and `securely` (your container is well isolated from the host, no need to share the docker socket to build a docker image).

Unfortunately, `sysbox` fails to install on WSL2 as it relies on systemd. See the [issue on github](https://github.com/nestybox/sysbox/issues/32
), where `sysbox` clearly wants to support WSL2. Microsoft may come first and support `systemd`.

# Manage your WSL2

What if to provision a standard WSL2 to other employees? Or to a different laptop?

&rarr; Simply export, import.

# Create your own Linux

1. Either import [a tar generated from a container](https://docs.microsoft.com/en-us/windows/wsl/use-custom-distro),
2. Or recompile the Linux kernel, [for ex to add ZFS modules](https://wsl.dev/wsl2-kernel-zfs/).

# WSL2 : What's next ?

For me:
- The biggest weakness of WSL2 is its network management.
- Having systemd support would be great.
- Having Windows Pageant integrated within WSL2 like within GitBash would be better than a solution plus 2 workarounds.
- Optional: Make WSL2 aware of all SSL certificates (public and private ones) saved into the Windows store could be nice.
- Optional: Make WSL2 aware of all credentials saved into the Windows store could be nice (ex: for git clone using https).
- More options to limit the resources like the disk.io like in Hyper-V.
- I'm looking forward Windows 11 which seems to come with a native X server named WSLg.

For others (few relevant issues I collected):
- Several users ask for a better support of Windows 11.
- A better support of mounts, like to access USB drive from WSL2.
- Be able to authenticate to WSL2 using Windows credentials.
- Protect the WSL part of Windows.

# In short

- Linux pushes the limits of the possible.
- Windows is your favorite development environment.
- WSL2 provides 2 but 1 machine.

# But wait

DevOps is not only technical:
- Believe in your dreams (like be here is special to me)
- Keep inspired by your mentors (I have several here)
- Grow with people who trust/encourage you (like the people who helped me for this pres)

# Thanks, you

You make it possible. Especially I would like to thanks:
- [Matthieu](https://www.linkedin.com/in/robinmatthieu/), [Matteo](https://www.linkedin.com/in/mazzeri/), and DevOps days Geneva team
- [Nicolas](https://www.linkedin.com/in/nicolaspangaud/), and hackathon team @Nagravision, where I could prepare this pres
- [David](https://www.linkedin.com/in/dalvarezquiroga/), [Bishwa](https://www.linkedin.com/in/bishwa-shrestha-37852253/), and [Jorge](https://www.linkedin.com/in/jorgeacarrasco/), who helped me to build this pres
- The DevOps community, like a family !!!
