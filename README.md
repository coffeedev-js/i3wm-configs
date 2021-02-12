# i3wm-configs
## My config files for i3wm
### Notice: I am not responable for anything that happens to your system.
### Always cjeck the packages or repos from their respective source
### Never copy & paste into terminal without any understanding
### Especially when it comes to the GPU passthrough part, be cautions, i warranty nothing, it can get complicated
### This readme is not a tutorial, just a guide for myself should i start to forget the knowledge i accumulated so hardly

///////////////////////////////////////////////////////
//Quick summary for fast-ricing your epic desktop    //
///////////////////////////////////////////////////////

1. Install i3wm-gaps preferably
add-apt-repository ppa:kgilmer/speed-ricer
apt-get update
apt intsall i3-gaps

1.1 Install timeshift
https://github.com/teejee2008/timeshift
It's your guardian angel should it work

2. Install the ttf fonts

3. For laptops, get brightnessctl to change display brightness
-> https://github.com/Hummer12007/brightnessctl

4.Pulsemixer, cool tty audio control
-> https://github.com/GeorgeFilipkin/pulsemixer

5. Install pavucontroll for good audio source control
apt-get install pavucontrol

6. Cava, cool tty audio visualizer
-> https://github.com/karlstav/cava

6.1 (If interested, get Tauon, cool music player)(Is a flatpack!)
-> https://github.com/Taiko2k/TauonMusicBox

7. Get feh, best image viewer
apt-get install feh

8. Epic borderless video player that can even stream videos from YT (Dependency: youtube-dl)
apt-get install mpv 

9. youtube-dl
https://github.com/ytdl-org/youtube-dl

10. Compton (Compositor) a must have!
apt-get install compton
apt-get install compton-conf (for painless gui configuration experience)

11. Kitty Terminal emulator, lightweight & fast terminal emulator
apt-get install kitty

12. Neofetch (The cooler Danie- eh tty infoscreen to show of your rig)
apt-get install neofetch

13. Ranger tty file browser
https://github.com/ranger/ranger
(Note for displaying images in terminal:
-Notice the .rc config file for Ranger.
-Also needs a tty emulator like kitty or alacritty that can utilize w3m
-Get w3m to display images in terminal: apt-get install w3m
-Also depends on imagemagick: apt-get install imagemagick imagemagick-doc
)

14. Make life easier with the Rofi menue
apt-get install rofi

15. Get the nvidia propriety drivers if you use ultra wide screens and or want to play with fan speeds:
apt-get install nvidia-settings 

Nvidia set fan speeds (Proprietary only) (Options are hidden until you set the coolbits)
sudo nvidia-xconfig --cool-bits=4

16. There is currently no Github desktop for Linux, but shiftkey has an absolute epic fork for Linux
https://github.com/shiftkey/desktop

///////////////////////////////////////////////////////
//Speed GPU passthrough summary for maximum fastness //
///////////////////////////////////////////////////////

>Requires IOMMU compatable hardware
>Requires hardware that utilizes AMD/Intel virtualisation
>Requires two GPUs
>Make yourself a favor and get decent hardware, virtualisation needs power!
>Obiously the .iso files of the operating systems
>You get trouble if you have two exact same graphics cards (IOMMU groups and IDs lol)
>Pref. a second monitor and video cable

Prereqs:
-Enable virtualisiation in your UEFI
-Enable IOMMU in your UEFI

1.Get the emulation necessities
apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virt-manager ovmf

3.Get the IDs of your graphics card
-In terminal: lspci -nn
-Search for the graphics card you want to passthrough and get the IDs, note them down somewhere
-The ID Looks something like "[GeForce RTX 2070]  [XXXX:XXXX]"
-Get ID of the Audio device of the same graphics card
-Looks something like "Audio Controller [XXXX:XXXX]" (And is one line beneath the card)

2. Edit your grub file at:
-nano /etc/default/grub
-Search for the line that says: "quite splash" and edit it to the following:
"quite splash amd_iommu=on kvm.ignore_msrs=1 vfio-pci.ids=XXXX:XXXX,XXXX:XXXX"
on Intel CPUs:
"intel_iommu=on kvm.ignore_msrs=1 vfio-pci.ids=XXXX:XXXX,XXXX:XXXX"
Where the Xs are the graphics card and audio IDs
-Save it and update grub with "update-grub" in terminal
NOTICE: The card which ID got written down won't be aviable for the Host system from now on!

3.Reboot

4. Check if the now excluded card is now used by the vfio-pci driver
-In terminal type lspci -k
-Search for the card
-At "Kernel driver in use: " it should say "vfio-pci"
-Same for the audio

5. Set up virt manager
-Create a new VM with preffered OS
-It is pretty straight forward, but dont forget to:
-Set Firmware under overview to: "UEFI" (usually the first in drop down)
-Add Hardware through the hardware button, such as your mouse & keyboard
-Add under "PCI Host Device" in "Add Hardware" the desired Graphics Card and it's audio controller
-Change under "Boot options" the boot order to the CDROM for installation purposes
-Install OS
-Once you end up in the shell, shutdown/force shutdown if needed the VM
-Change to boot order again to the SATA drive, since the installation media is not needed anymore

6. Complete the passthrough
-In terminal, type: virsh edit [name of the virtual machine] (for example: virsh edit win10)
-Pick your editor
-Copy following XML:

<hyperv>
  <vendor_id state='on' value='whatever'/>
<hyperv>

-Paste it after the

<acpi/>
<apic/>

closing tags
Then copy and paste following XML right after the hyperv tags

<kvm>
<hidden state='on'/>
</kvm>

-Save the file and start the VM, you should be ready to go.
