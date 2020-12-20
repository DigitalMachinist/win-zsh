# win-zsh

A guide for setting up the nicest terminal Windows has ever had.

![Yes, this is possible in Windows now.](https://raw.githubusercontent.com/DigitalMachinist/win-zsh/main/featured.png)

This is my Windows Terminal app running oh-my-zsh with the [powerlevel10k](https://github.com/romkatv/powerlevel10k) theme installed. I don't provide instructions on how to set this *specific* theme up, however the configuration tool that comes with powerlevel10k is *fantastic* and it's easy to get a terminal set up that looks and behaves as nicely as what I have here once you finish this guide.

## What am I looking at?

**Finally, it's possible to have a decent Win10 terminal.**

In this guide, my intent is to install a `bash` & `zsh` terminals onto a Windows 10 machine such that they:

- Have easy access to the Windows filesystem
- Run almost as fast as a native implementation
- Have a good baseline vim config without additional work
- Support copy/cut/paste with `Ctrl+C`, `Ctrl+X`, `Ctrl+V` like normal
- Depend on only software that I have used and know works well
- Support colour themes to liven up your terminal!

I'm leaning on [Microsoft's WSL install guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10) and it might be a valuable resource if you run into trouble. Also, [this deep dive into WSL is very helpful](https://adamtheautomator.com/windows-subsystem-for-linux/) if you want to get into the nitty gritty.

## Phase 1: Ubuntu bash via WSL

In the first phase of this process, we're going to install Windows Subsystem of Linux and get Ubuntu up and running within our Windows install so we have access to a bash terminal. This is already great, but is only a first step toward better things.

You can install just WSL1 or upgrade to WSL2. I use the older WSL1 personally, because WSL1 offers better Windows filesystem performance and all the files I work on are on my Windows drive. However, WSL2 may perform better if you keep the files you work with on your Linux filesystem. See [this comparison](https://docs.microsoft.com/en-us/windows/wsl/compare-versions) for more details. It is worth noting that it's possible to use both versions on the same machine, but that's beyond the scope of this guide.

1. [Open the Windows Store app and install the Ubuntu app](https://www.microsoft.com/en-ca/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab) by Canonical Group Limited. If the Windows Store isn't available to you, you can [enable developer mode](https://docs.microsoft.com/en-us/windows/apps/get-started/enable-your-device-for-development) and manually install the Ubuntu 20.04 app from [here](https://docs.microsoft.com/en-us/windows/wsl/install-manual). 

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
    ln -s /mnt/c/Users/<your Windows username> ~/win10
    ```

8. Congratulations! You can use bash from Windows. It's not a trick or a hacky Windows implementation. Real Linux bash, with access to all the great tooling from the Ubuntu/Debian environment. The possibilities are endless! But we're only getting started...

## Phase 2: New Windows Terminal

In the past, I used [ConEmu](https://conemu.github.io/) or [Cmder](https://cmder.net/) as a terminal app to centralize all of my terminals to one window. It's possible to run WSL terminals on apps such as these, but the setup is more complex than it needs to be. These days, the new [Windows Terminal](https://github.com/microsoft/terminal) from Microsoft acts to merge all my terminals into one window and has out-of-the-box support for WSL and will run the Ubuntu bash terminal we installed above with zero configuration.

1. [Open the Windows Store app and install the Windows Terminal app](https://www.microsoft.com/en-ca/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab) by Microsoft. If the Windows Store isn't available to you, [the Windows Terminal Github repo](https://github.com/microsoft/terminal#installing-and-running-windows-terminal) offers some other installation options to try.

2. Run Windows Terminal. It should launch a PowerShell terminal by default, but the new terminal dropdown will include an option for `Ubuntu`. Open an `Ubuntu` terminal to launch your WSL Ubuntu bash from Phase 1 to confirm that everything is working so far.

3. Open your Windows Terminal settings and compare them with [settings.json](https://github.com/DigitalMachinist/win-zsh/blob/main/settings.json) in this repo. Feel free to copy my `settings.json` over your own (make sure that your profile `guid` fields don't get lost when you do) or take bits and pieces that make sense for you. Check out [the documentation](https://docs.microsoft.com/en-us/windows/terminal/customize-settings/global-settings) for more info about configuring Windows Terminal.

4. Nice! All your terminal windows belong to a simple tabbed interface and you can get to Ubuntu bash easily. Now it's time to do what we all came here for...

## Phase 3: Oh My Zsh

Here's the fun part. Let's get zsh installed and then spend hours fussing with it to make it pretty!

1. Open a new `Ubuntu` tab in Windows Terminal.

2. Run the following command at your `bash` terminal to install `zsh`:

    ```bash
    sudo apt-get install zsh
    ```
    
3. Run the following command at your `bash` terminal to set `zsh` as the default terminal.

    ```bash
    chsh -s $(which zsh)
    ```

4. Open a new `Ubuntu` tab in Windows Terminal. You will be presented with the zsh setup wizard. I leave the details of this setup to you to figure out. It's your terminal!

5. Once the zsh configurator is finished, you should be sitting on a zsh terminal. Run `ps` at the terminal and if `zsh` is one of the active processes, then your're golden! 

6. Run the following command at your zsh terminal to install `oh-my-zsh`:

    ```zsh
    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    ```

7. If you want to install a terminal theme, you can choose from the [available options](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) then edit the value of the `ZSH_THEME` variable in your `~/.zshrc` to the name of your chosen theme. There are [even more available options](https://github.com/ohmyzsh/ohmyzsh/wiki/External-themes) but you'll need to download these separately to use them. I like [powerlevel10k](https://github.com/romkatv/powerlevel10k) myself, but this is an exercise for the reader.

8. Congratulations! You might just have the nicest terminal that's ever been available on a Windows machine! Time to install weird gizmos to impress your friends...

## Finishing Touches

The instructions above cover the necessary steps to get zsh working and looking pretty, but there are some more things you might want to do to get the most out of this setup.

- Set up your `Ubuntu` terminal as the default for new terminal windows in Windows Terminal by setting the `defaultProfile` in `settings.json` to whatever the `guid` of your `Ubuntu` profile is.

- Set your `startingDirectory` in `settings.json` to `%USERPROFILE%` or a folder inside it so `zsh` opens into the Windows filesystem by default. It's less confusing to keep my files there in the idiomatic Windows folders than inside the filesystem of my WSL Ubuntu disk (which is basically hidden in Windows).

- Consider mapping `\\wsl$\Ubuntu\` as a network drive. This will allow you to quickly and easily navigate to your Ubuntu root folder (`/`) and find files from Windows. Just type `\\wsl$` into explorer, right-click on the Ubuntu folder, select `Map network drive` and pick your favourite letter for the drive.

- Windows Terminal's settings allow you to set a colour theme that interferes with the colours of oh-my-zsh themes. You might need to fuss with these to get the best look. I haven't figured out how to guarantee that you're getting the colours that oh-my-zsh theme authors intended for their themes just yet.

- Consider installing a nice monospace font for your new terminal. Here are a few recommendations:
  - [MesloLGS NF](https://github.com/romkatv/powerlevel10k/blob/master/font.md) (Use this if you install the powerlevel10k Oh My Zsh theme!)
  - [Ubuntu Mono](https://fonts.google.com/specimen/Ubuntu+Mono)
  - [Fira Code](https://github.com/tonsky/FiraCode)

- Consider [upgrading your WSL Ubuntu from 20.04 to 20.10](https://discourse.ubuntu.com/t/installing-ubuntu-20-10-on-wsl/18941). It really does open up some exciting new possiblities for what you can do with it (like some of the recommended tools below).

- Consider adding some other spicy *nix tools to your Windows terminal:
  - [bat](https://ostechnix.com/bat-a-cat-clone-with-syntax-highlighting-and-git-integration/) is a more colourful and usable version of `cat`
  - [exa](https://github.com/ogham/exa) is a more colourful and usable version of `ls`
  - [fd](https://github.com/sharkdp/fd) is a more colourful and usable version of `find`
  - [httpie](https://github.com/httpie/httpie) is a more colourful and usable version of `curl`
  - [fzf](https://github.com/junegunn/fzf) is a powerful fuzzy-search tool. In particular, it supports keybinds such as `Ctrl+R` to fuzzy search your command history or `Alt+C` to fuzzy-find files recursively from your current folder.

- I like to add the following lines to the end of my `~/.zshrc` to automatically start up my SSH agent and install my SSH key so I can use Github via SSH without interruption:

    ```zsh
    echo "Starting SSH agent..."
    eval $(ssh-agent -s)
    ssh-add ~/.ssh/<private key file>
    ```
    
- I'm annoyed by how my Windows Terminal `Settings.json` copy/paste bindings collide with \*nix commands and would love to have better suggestions. For example, in vim `Ctrl+V` enters visual mode but Windows Terminal's `Ctrl+V` bind seems run the equivalent of `xp` in vim (delete the current character and replace it with the copied text) which prevents you from entering visual mode entirely *and* unexpectedly changes your document.

- While I don't use PowerShell that much personally, [PowerLine](https://github.com/Jaykul/PowerLine) seems like a promising tool to bring similar terminal theming to that of oh-my-zsh with powerlevel10k to PowerShell. Check that out if you're a regular PowerShell user.

## Issues

I'm offering this guide without warranty and I realize that eventually it will become obsolete somehow, so if you run into trouble I can't guarantee that I can help. That being said, I encourage people to post issues on this repo if you encounter problems or even if you just think something could be configured better or explained more easily. Please share your experience so I can attempt to make this guide better and learn from you.
