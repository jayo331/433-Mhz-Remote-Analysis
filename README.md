# Garage door reverse engineering


## General Note:
This is not the usual replay based attack. The article does not contain any copy and paste script, this is only a proof of concept. The idea behind this article is only to play around with SDR and radio protocols in general. Even if my device is a little bit old, similar newer systems use similar transmission protocols, also since this type of device is not regularly updated this vulnerability still affects a lot of buildings. 

## Tools used:
The used hardware is: an RTL-SDR dongle, antenna and the remote that i want to investigate. As the antenna, i used the dipole that comes with the original RTL-SDR kit tuned to match the remote control frequency. For the software, i used Universal Radio Hacker.

## Device info:
The remote has two programmable channels, to pair the remote with the respective door mechanism a 10-pin dip switch is used. After the user sets the same switch combination both on the remote and the door mechanism when the button is pressed the door will be automatically open or close.

## The weakness:
Since the dip switch has two states (ON/OFF) the total possible combinations are 2^10 (1024). By hands, a brute-force attack will certainly
require too much time but once the protocol is successfully reversed we can automate the process by using an Arduino paired with a 433MHz transmitter.

## Analysis:
After some spectrum investigation, i found that my remote operates in the 433.92 MHz region. Once the correct frequency was set in the software, i recorded a 5s sample. After some visual analysis, i found that when the button is pressed the remote sends a continuous pulse train of a fixed message. ASK modulation is used and the message is Manchester I encoded. The message analysis is now very simple, after isolating the single packet i found that each of them is 21-bit long, the first bit is always zero (preamble), the variable data field is 10-bit long and it directly contains the information about the dip switch position, a fixed 5-bit padding is added and then a zero. Between each message a fixed 4-bit "space" is added, then the message is repeated.

![Packet](link stringa)

## Conclusion:
Since each packet requires around 110ms to be transmitted it is possible to brute-force all the combinations in around 112.64s, surely re-transmission will slow down the process however even by counting re-transmission, a complete brute-force will take less than 6 minutes. It is worth mentioning that due to the simplicity of the protocol, no transmission timeout was considered, to confirm this further tests should be made. At this point making an Arduino script that accomplishes a brute-force attack should be an easy thing however, since my primary goal was not to open random people's doors, i'm not interested in developing further this concept.
