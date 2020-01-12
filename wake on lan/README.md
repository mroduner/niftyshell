# HAXBITS Wake on Lan Tools 
Use and abuse instructions for using ancient technologies with libvirt/KVM+QEmu

## Installation Pre-Requisites
 * Any LSB compliant linux distro
 * php7-cli
 * php-libvirt-php (only for the listen script)

## Set Up
```
$ [ -d ~/bin ] || mkdir ~/bin
$ pushd ~/bin
$ wget -O wakeup "https://raw.githubusercontent.com/mroduner/niftyshell/master/wake%20on%20lan/wakeup" 
$ wget -O listen "https://raw.githubusercontent.com/mroduner/niftyshell/master/wake%20on%20lan/listen"
$ chmod a+x,og-w ./listen ./wakeup
$ popd
$ source ~/.profile
```

## Using the listener
  The script `listen` accesses your local libvirt installation, and reads the MAC addresses of all domains avaliable on your local hypervisor (tested using KVM+Qemu, should be agnostic though).  IT then listens on UDP port 4343 for a Wake on Lan magic packet, and upon finding a matching entry, will start the domain for you.
  
  Simple usage `$ listen`; that easy
  
  Advanced Usage: `$ listen --network==10.0.42.255 --port=4343`
   * **network** is settable if you only want to listen to one NIC, must be in dotted decimal, and there's really not a good reason to use this.
   * **port** if your existing WOL tools need a different port than 4343, set that here
   
## Wake the (_mostly_) Dead
  The script `wakeup` sends a magic packet that should be compatible with both real and well, the above script WOL listener.  It will automatically try to find the proper broadcast addresses automagically for all of the visible network cards on your system.  Since most routers will drop overbroad broadcasts (for really good reason), we set up the broadcast packets to match your netmask (_if your local machine is 192.168.3.42/22, the mask would be 192.168.3.255, not the global 255.255.255.255_) 
  
  Simple Usage `$ wakeup mac_address [... mac address]`
  
  Compicating things way too much `$ wakeop --network=172.6.12.255 --port=4343 mac_address [... mac address]`
  * **network** should not be specified, but if you really want this, set it to the boradcast address you want to use to force communication to a single interface.
  * **port** This must match the listeners port selection for listen to properly accept the packets, but hardware WOL implementations often ignore the port.  Leave this alone unless you know what you're doing.
  * **mac_address** a mac address in either plain hex, windows, or *nix format.  you can put as many entries in the list that you want to.
  
### Examples
Waking up 90's hackers `$ wakeup --port=0xfeed 12:ca:fe:ba:be de:ad:be:ef:f0:0d`

