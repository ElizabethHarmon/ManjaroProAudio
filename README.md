# A Pro Audio Tuning Guide for Manjaro (and other Arch-based distros)

Following this guide will hopefully allow you to get the best possible performance on Linux for professional audio needs. Even though these steps are well-tested, it is wise to research what each step accomplishes and why (the search engine is your friend :P ).

## Fundamentals

To get started after installing Manjaro, you could try just steps 3 and 5 below. If you need to use windows plugins on Linux also follow step 11 (easy: wine-staging, more advanced but potentially more performance: wine-tkg). Based on your individual pro audio needs, workflows, hardware specifications and more, your mileage may vary. If you are still having audio performance issues, try following the full guide...

## Full In-depth Guide

1) Install Manjaro KDE (or other favorite Arch-based distro)

2) rtcqs (formerly known as realtimeconfigquickscan)
    ```shell
    git clone https://codeberg.org/rtcqs/rtcqs.git
    cd rtcqs
    ./rtcqs.py
    ```

3) install realtime-privileges
    ```shell
    yay -S realtime-privileges
    ```
    Add user to "realtime" group and "audio" group (to satisfy realtimeconfigquickscan)
    ```shell
    sudo usermod -a -G realtime,audio $USER
    ```
    Log out/in or reboot...

4) add "threadirqs" as kernel parameter
    ```shell
    sudo nano /etc/default/grub
    ```
    change 
    `GRUB_CMDLINE_LINUX=""` to `GRUB_CMDLINE_LINUX="threadirqs"`
    
    ```shell
    sudo update-grub
    ```

5) Set governor to "performance"

    i. Temporary:
    ```shell
    sudo cpupower frequency-set -g performance
    ```
    ii. Permanent:
    
    Add `cpufreq.default_governor=performance` as a kernel parameter:
   
    ```shell
    sudo nano /etc/default/grub
    ```
    LIne should now read: 
     `GRUB_CMDLINE_LINUX="cpufreq.default_governor=performance threadirqs"`
     ```shell
    sudo update-grub
    ```
    or, for kernels < 5.9:
    ```shell
    sudo nano /etc/default/cpupower (uncomment governor and change to performance)
    systemctl enable --now cpupower.service
    systemctl start cpupower.service
    ```


6) swappiness
    ```shell
    sudo nano /etc/sysctl.d/99-sysctl.conf
    ```
    add "vm.swappiness=10"

7) base-devel (as necessary)
    ```shell
    sudo pacman -S base-devel
    ```

8) install udev-rtirq
    ```shell
    git clone https://github.com/jhernberg/udev-rtirq.git
    cd udev-rtirq
    sudo make install
    reboot
    ```

9) Jack2 + Jack D-Bus
    ```shell
    yay -S qjackctl jack2-dbus
    ```
    Enable Jack D-Bus interface:  
    ![image](https://user-images.githubusercontent.com/79659262/124497122-51218300-ddb2-11eb-8cb8-4bf873e026cd.png)


10) DAW & Plugins

    REAPER: 
    http://reaper.fm/download.php or,  
    ```shell
    yay -S reaper-bin
    ```
    change RT priority to 40 on audio device page?  
    
    
    Also be sure to check out Bitwig Studio, Tracktion Waveform, Ardour, Mixbus, Qtractor, LMMS, Rosegarden, Zrythm etc...
    https://en.wikipedia.org/wiki/List_of_Linux_audio_software#Digital_audio_workstations_(DAWs)

* Native plugins (`yay -S [pkgname]`)
  * x42-plugins (https://x42-plugins.com/x42/)
  * airwindows-git (http://www.airwindows.com/)  
  * lsp-plugins  (https://lsp-plug.in/)
  * zam-plugins  (http://www.zamaudio.com/?p=976)
  * distrho-ports (https://distrho.sourceforge.io/ports.php)
  * dpf-plugins (https://distrho.sourceforge.io/plugins.php)
  * dragonfly-reverb (https://michaelwillis.github.io/dragonfly-reverb/)
  * Bertom Denoiser (https://www.bertomaudio.com/denoiser.html)
  * sfizz / sfizz-git (https://sfz.tools/sfizz/)

11) Wine-staging or Wine-tkg

    ```shell
    yay -S wine-staging
    ```

    Or, install a particular version that you know is compatible:
   
    ```shell
    yay -S downgrade
    sudo DOWNGRADE_FROM_ALA=1 downgrade wine-staging
    ```
    
   Check https://github.com/robbert-vdh/yabridge#tested-with for up-to-date info.
    
   OR...for the more adventurous:
    
   either download wine-tkg from https://github.com/Frogging-Family/wine-tkg-git/releases or follow the instructions to git clone and install latest version: https://github.com/Frogging-Family/wine-tkg-git/tree/master/wine-tkg-git#quick-how-to-
       
12) Install yabridge

    ```shell
    yay -S yabridge-bin
    ```
    or for latest git:
    ```shell
    yay -S yabridge-git yabridgectl-git
    ```
    
    iii. Configure yabridge according to https://github.com/robbert-vdh/yabridge#readme  
    iv. if using wine-tkg, append "export WINEFSYNC=1" to ~/.bash_profile  
    ```shell
    nano ~/.bash_profile
    . ~/.bash_profile
    ```
    
    Or, if your shell is not bash, use the guide here: https://github.com/robbert-vdh/yabridge#environment-configuration
    
    v. Install Windows VST2 and VST3 (preferred) plugins
    
    
   ### Thanks
   Robbert van der Helm (of yabridge fame) for pointing out typos/omissions and suggesting alternative methods. JamesPeters (REAPER forums) for suggesting expansion of steps 3, 4 & 5. graves501 for rtcqs info update, audiojunkie for the latest title, Damien Zammit (of zam-plugins fame) and MikeLupe for discussion of other Linux DAWs including Ardour and Zrythm.




