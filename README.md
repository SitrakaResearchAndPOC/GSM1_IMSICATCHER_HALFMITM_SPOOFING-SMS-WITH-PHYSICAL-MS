# GSM_IMSI_CATCHER_HALF_MITM
* [motorola](https://www.youtube.com/watch?v=ZKa86zAWmQY&pp=ygURZ3NtIHNuaWZmaW5nIDI5YzM%3D) documentation
* [spoofing](https://github.com/godfuzz3r/osmo-nitb-scripts/tree/master)
* [phddays]()
# Half MITM = Fake BTS only
# Why it is possible ?
* Fake base station with open ciphering named A5/0, and the  network could be GSM(sms, call), and GPRS, EDGE

# Attack limitations and advantages 
* local attack but could be targeting (to find local victim)
* victim could be reached on the real network (indeed call and sms)
* could be detectable with rooted phone and application like imsi-catcher detector, snoopsnitch because of open network

# Devices  
* USRP
* motorola phone (aka calypso phone on google)
* BladeRF
* LimeSDR
  
# Pratices
* Install DragonOS 29 or 30
* Create fake bts with open ciphering (A5/0)
* Add one users and modify core network for associating it with number 0341220590
* Send message from 0341220590 to 0341220590
* Send broadcast message

# Command you need : 
```
dmesg | grep ttyUSB*
```

# Solutions :
* Using USRP (more stable and no need synchronization of existing BTS so if we jam the existing BTS the half mitm still exists):
```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/nitb-script-all/main/osmo-nitb-scripts.zip
```
```
unzip osmo-nitb-scripts
```
```
cd osmo-nitb-scripts
```
