# win-zsh

A guide for setting up the nicest terminal Windows has ever had.

![Yes, this is possible in Windows now.](https://raw.githubusercontent.com/DigitalMachinist/win-zsh/main/featured.png)

## What am I looking at?

**Finally, it's possible to have a decent Win10 terminal.**

In this guide, my intent is to install a bash & zsh terminals onto a Windows 10 machine such that they:

- Have easy access to the Windows filesystem
- Run almost as fast as a native implementation
- Have a good baseline vim config without additional work
- Support copy/cut/paste with `Ctrl+C`, `Ctrl+X`, `Ctrl+V` like normal
- Depend on only software that I have used and know works well
- Support colour themes to liven up your terminal!

This process is based on [Nuno Caneco's guide](https://medium.com/@nuno.caneco/zsh-running-on-cmder-498353a1495d) which references [Sebastian Gingter's guide](https://gingter.org/2016/08/17/install-and-run-zsh-on-windows/), plus a hack to make it all work under Windows Subsystem for Linux 2 (WSL2) [that you can find here](https://github.com/Maximus5/ConEmu/issues/1930#issuecomment-646808989).

I'm also leaning on [Microsoft's WSL install guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10) and it might be a valuable resource if you run into trouble.

Note: The screenshot above is of my Windows terminal running oh-my-zsh with the [powerlevel10k](https://github.com/romkatv/powerlevel10k) third-party theme installed and configured. I don't provide instructions on how to set this theme up, however the configuration tool that comes with powerlevel10k is *fantastic* and it's pretty easy to get a terminal set up that looks and behaves as nicely as what I have here.

## Phase 1: WSL Bash

In the first phase of this process, we're going to install Windows Subsystem of Linux and get Ubuntu up and running within our Windows install so we have access to a bash terminal. This is already great, but is only a first step toward better things. 

You can install just WSL1 or upgrade to WSL2. I use the older WSL1 personally, because WSL1 offers better Window filesystem performance and all my files are on my Windows drive, but it is important to note that WSL2 may perform better if you keep your dev files on your Linux filesystem. See [this comparison](https://docs.microsoft.com/en-us/windows/wsl/compare-versions) for more details. It is worth noting that it's possible to use both versions on the same machine, but that's beyond the scope of this guide.

1. Open the Windows Store app and install the Ubuntu app by Canonical Group Limited. If the Windows Store isn't available to you, you can [enable developer mode](https://docs.microsoft.com/en-us/windows/apps/get-started/enable-your-device-for-development) and manually install the Ubuntu 20.04 app from [here](https://docs.microsoft.com/en-us/windows/wsl/install-manual). 

2. Open a Powershell terminal as Administrator.

3. Run the following command in a PowerShell terminal to install WSL:

    ```Powershell
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    ```

4. If you want to upgrade to WSL2:

    1. Run the following command in a PowerShell terminal to install VM features you'll need:
    
        ```Powershell
        dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
        ```
    
    2. Download and install the [WSL2 Linux kernel update](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) from [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package).
    
    3. Run the following command in a PowerShell terminal to switch to WSL2 by default:
    
        ```Powershell
        wsl --set-default-version 2
        ```

5. Reboot your computer. **Yes, really.**

6. After rebooting, run the Ubuntu app to confirm that a bash terminal starts up. You may need to set a username & password for your Ubuntu user at this point.

7. Run the following command at your Ubuntu bash terminal to create a symlink from your Ubuntu home (~) to your Windows user folder:

    ```bash
    ln -s ~/win10 /mnt/c/Users/<your Windows username>
    ```

8. Congratulations! You have bash on Windows. But we're only getting started...

## Phase 2: Cmder Bash

1. Download the full version of Cmder ([https://cmder.net/](https://cmder.net/))

2. Cmder is a portable app, so unzip it to any folder you want it to live in. I typically use `C:\Users\<username>\.portable` for my portable apps (so `C:\Users\<username>\.portable\Cmder` in this case). You'll probably also want to send a shortcut to `Cmder.exe` to your desktop or link to it in some other way for convenience. I always set this shortcut to "Run as Administrator" but I'm not sure this is required.

3. Run Cmder and watch its default terminal work. We're going to ignore that from here on out. Click the green plus to open a new terminal and select `{WSL::bash}`. If you get an error like [this](https://conemu.github.io/en/BashOnWindows.html), you need to follow step 4 (otherwise you can skip to step 5):

    ```Plaintext
    wslbridge error: failed to start backend process
    note: backend error output: -v: -c: line 0: unexpected EOF while looking for matching `''
    -v: -c: line 1: syntax error: unexpected end of file

    ConEmuC: Root process was alive less than 10 sec, ExitCode=0.
    Press Enter or Esc to close console...
    ```

4. If Cmder errors out on WSL Bash, you need to replace the wslbridge that failed in the error with wslbridge2. The Github issue comment where the following solution is invented is [here](https://github.com/Maximus5/ConEmu/issues/1930#issuecomment-652326745) if you want context. The solution goes like so:

    1. Recall the folder you unzipped Cmder to. Use explorer to navigate to `<your Cmder path>\vendor\conemu-maximus5\ConEmu\wsl\` and rename `cygwin1.dll` to `cygwin1-backup.dll`. We're going to replace this file but keeping a backup never hurts.

    2. Download the latest release of wslbridge2 from [https://github.com/Biswa96/wslbridge2/releases](https://github.com/Biswa96/wslbridge2/releases). I downloaded the `x86_x64` version of release `v0.7` but newer versions will probably work fine (famous last words?).

    3. Extract `wslbridge2_cygwin_x86_64.zip` to `<your Cmder path>\vendor\conemu-maximus5\ConEmu\wsl\`. Note that this creates a new `cygwin1.dll` that we're going to overwrite.

    4. Delete the newly extracted `<your Cmder path>\vendor\conemu-maximus5\ConEmu\wsl\cygwin1.dll`. We don't need to back this up, but you can rename it instead if you're paranoid.

    5. Go to [https://cygwin.com/snapshots/](https://cygwin.com/snapshots/) and download the latest `cygwin1-XXXXXXXX.dll.xz`. I downloaded `cygwin1-20200909.dll.xz` but newer versions will probably work fine (famous last words?).

    6. Use 7-Zip to open your `cygwin1-XXXXXXXX.dll.xz` and extract it to `<Cmder path>\vendor\conemu-maximus5\ConEmu\wsl\`.

    7. Rename `cygwin1-XXXXXXXX.dll` to `cygwin1.dll`.

5. In Cmder, press `Win+Alt+T` to open the Tasks Settings.

6. Select the `{WSL::bash}` task and edit its command (the bottom text field) to the following:

    ```bash
    set "PATH=%ConEmuBaseDirShort%\wsl;%PATH%" & %ConEmuBaseDirShort%\conemu-cyg-64.exe %ConEmuBaseDirShort%\wsl\wslbridge2.exe - -cur_console:t:bash cur_console:pm:/mnt -eConEmuBuild -eConEmuPID -eConEmuServerPID -l -W ~/win10
    ```

7. Exit the settings and open a new `{WSL::bash}` terminal again. It should open to a terminal without errors this time.

8. Congratulations! You have a pretty solid bash with decent colouring and vim defaults for Windows. But we can go deeper...

## Phase 3: Oh My Zsh

1. Open your new `{WSL::bash}` terminal in Cmder.

2. Run the following command at your bash terminal to install `zsh`:

    ```bash
    sudo apt-get install zsh
    ```

3. Edit your `~/.bashrc` file (I use vim but using whatever *nix text editor you like) to include the following, right at the top of the file and save it:

    ```bash
    if test -t 1; then
        exec zsh
    fi
    ```

4. Open a new `{WSL::bash}` terminal. You will be presented with the zsh setup wizard. I leave the details of this setup to you to figure out. It's your terminal!

5. At this point, you should be sitting on a zsh terminal. To prove it, you can run `ps` and if `zsh` is one of the active processes then your golden! 

    1. You may want to rename your `{WSL::bash}` terminal to `{WSL::zsh}` in Cmder.
    
    2. You can also replace `cur_console:t:bash` in the command string with `cur_console:t:zsh` so the terminal tab is named correctly in Cmder.

6. Run the following command at your zsh terminal to install `oh-my-zsh`:

    ```zsh
    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    ```

7. If you want to install a terminal theme, you can choose from the [available options](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) then edit the value of the `ZSH_THEME` variable in your `~/.zshrc` to the name of your chosen theme. There are [even more available options](https://github.com/ohmyzsh/ohmyzsh/wiki/External-themes) but you'll need to download these separately to use them. I like [powerlevel10k](https://github.com/romkatv/powerlevel10k) myself, but this is an exercise for the reader.

8. Congratulations! You might just have the nicest terminal that's ever been available on a Windows machine!

## Finishing Touches

The instructions above cover the necessary steps to get zsh working and looking pretty, but there are some more things you might want to do to get the most out of this setup.

- To make your WSL2 Ubuntu files more easily accessible from Windows, consider mapping `\\wsl$\Ubuntu\` as a network drive. This will allow you to quickly and easily navigate to your Ubuntu root folder (`/`). Just type `\\wsl$` into explorer, right-click on the Ubuntu folder, select `Map network drive` and pick your favourite letter for the drive.

- Cmder's general settings include a colour theme of its own that interferes with the colours of oh-my-zsh themes. You might need to fuss with these to get the best look. I haven't figured out how to bypass this yet.

- Definitely set up your `{WSL::zsh}` terminal as the default for new terminal windows in Cmder.

- Consider installing a nice monospace font for your new terminal. Here are a few recommendations:
  - [Fira Code](https://github.com/tonsky/FiraCode)
  - [MesloLGS NF](https://github.com/romkatv/powerlevel10k/blob/master/font.md)
  - [Ubuntu Mono](https://fonts.google.com/specimen/Ubuntu+Mono)

- Consider [upgrading your WSL2 Ubuntu from 20.04 to 20.10](https://discourse.ubuntu.com/t/installing-ubuntu-20-10-on-wsl/18941). It really does open up some exciting new possiblities for what you can do with it (like some of the recommended tools below).

- Consider adding some other spicy *nix tools to your Windows terminal:
  - [bat](https://ostechnix.com/bat-a-cat-clone-with-syntax-highlighting-and-git-integration/) is a more colourful and usable version of `cat`
  - [exa](https://github.com/ogham/exa) is a more colourful and usable version of `ls`
  - [fd](https://github.com/sharkdp/fd) is a more colourful and usable version of `find`
  - [httpie](https://github.com/httpie/httpie) is a more colourful and usable version of `curl`
  - [fzf](https://github.com/junegunn/fzf) is a powerful fuzzy-search tool. In particular, it supports keybinds such as `Ctrl+R` to fuzzy search your command history or `Alt+C` to fuzzy-find files recursively from your current folder.

- I like to add the following lines to the end of my `~/.zshrc` to automatically start up my SSH agent and install my SSH key so I can use Github without interruption:

    ```zsh
    echo "Starting SSH agent..."
    eval $(ssh-agent -s)
    ssh-add ~/.ssh/<private key file>
    ```

## Issues

I'm offering this guide without warranty and I realize that eventually it will become obsolete somehow, so if you run into trouble I can't guarantee that I can help. That being said, I encourage people to post issues on this repo if you encounter problems or even if you just think something could be configured better or explained more easily. Please share your experience so I can attempt to make this guide better and learn from you.
