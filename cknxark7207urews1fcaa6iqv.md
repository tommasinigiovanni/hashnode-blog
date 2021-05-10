## VoIP: fundamentals of SIP and related topics - part #4: UA PBX, standard configurations and some security tips

Here we are with the **fourth** appointment with the mini-series about VoIP.  
You can find the **last episode** here:

%[https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-3-sdp-and-rtp]

In this episode I'll quickly talk about the most common configurations of the PBX, including its security.

> A SIP *user agent* (**UA**) is a logical network end-point used to create or receive SIP messages and thereby manage a SIP session.  
A SIP UA can perform the role of a *User Agent Client* (**UAC**), which *sends SIP requests*, and the *User Agent Server* (**UAS**), which *receives the requests* and returns a SIP response. 
 These roles of UAC and UAS only last for the duration of a SIP transaction.

---

The most common configurations for a PBX used to make and receive VoIP calls are as follows:

- **PBX with public interface**: in this case, the PBX will usually have two network interfaces, a **private** one to communicate with the phones in the LAN, and a **public** one to communicate with the provider from which it purchases the VoIP numbers.  
In this configuration, the system administrator who manages the PBX will have to pay close attention to its security as it is exposed to attacks from the public network; however, problems related to the presence of private IPs in SIP packets or in the SDP will be rare (if in the SIP packets sent to the provider that provides the VoIP account, there are private IPs, you could run into calls with one-way audio or that do not take place).

- **PBX with private interface behind NAT**: in this case, the PBX has only 1 network interface with **private IP**. It is behind the management of a router that will populate the NAT tables and route the packets to and from the PBX.  
This type of configuration could be more secure but can generate **NAT traversal problems**. The problems of this type are due to the fact that the PBX having only a private interface, if not well configured, could forge its packets with private IP; the SIP proxy that will receive an INVITE with `Via`, `Contact`, and `SDP` with **private IP**, will have serious difficulties in managing the call in the best possible way.  
Some routers implement **SIP ALG mechanisms** to parse and rewrite SIP packet headers where necessary; however, these mechanisms usually do not work correctly and therefore it is preferable to disable them.  
Usually SIP proxies implement **NAT HELPER mechanisms**, which by comparing the IP of origin of the SIP packet with the IPs contained in its headers will understand if the UA that generated the request is behind NAT or not; if it understands that the PBX is behind NAT, it will attempt to correct the private IPs present in the headers.   
 [Kamailio](https://www.kamailio.org/w/) , a beautiful Open Source SIP Server, for example, has an entire module dedicated to the  [nathelper](https://www.kamailio.org/docs/modules/stable/modules/nathelper.html) , with functions such as  [fix_nated_contact()](https://www.kamailio.org/docs/modules/stable/modules/nathelper.html#nathelper.f.fix_nated_contact) that rewrites the "Contact" header.

Some PBXs (for example Asterisk) implement configurations and mechanisms for proper management behind NAT:
1. the **externip** (*external host*) parameter, to be added to the asterisk **sip.conf** indicates the IP that will be used as the source for all SIP messages when the PBX is behind NAT; configuring this parameter will allow asterisk to forge SIP packets correctly.
2. Another tipical mechanisms is the **STUN** (*Simple Traversal of UDP through NAT*). STUN is an industry standard approach for traversal of NAT and the technical details are published as RFC 3489.  
This mechanism relies on an external server (STUN server); the PBX, when it has to forge a SIP packet, will contact the STUN server; the STUN server will ask: Is the PBX behind a NAT device? What is the external IP address of the NAT device? It will then find the answers, and the external IP address of the PBX will be returned to the PBX; the PBX can then use it to correctly forge its SIP packets.

---

Nowadays any service exposed on the internet is subject to attacks by malicious users. If you have a PBX (at home or in the cloud), the illegitimate users and bots will repeatedly attempt to break into yours system trying to authenticate with different credentials.

## Security tips:
1. The first thing to do to secure the PBX is to **configure a
firewall** to protect it, inhibiting access to:
  - all unnecessary ports (DENY by default)
  - all unnecessary source IP addresses (put only the
IP from which your customer will have to access the switchboard).
2. **Apply restrictions at the source IP level**  
If you know where the packets will come from (VoIP operator, phones that connect from the outside) enables access only to IP addresses known.  
If, on the other hand, you do not have the possibility to know in advance the IP addresses source or ranges, use the GEOIP functionality to limit access to "your county" / European IPs only.
3. **Protect yourself with strong passwords**  
Use strong passwords (letters + numbers + special characters) of at least 12 characters for both SIP users and HTTP users (if access the WEB panel they can do what they want).
4. **International calls**  
If you don't need it, block your PBX from being able to do so calls to international numbers and / or extra-cost numbers.  
A possible alternative, if provided by the switchboard, is that of enable international calls by entering a PIN code.
5. **Anti-fraud**
An additional level of security is to be able to "close the gate when some oxen have escaped ". In this sense, use a VoIP provider which has automatic management within its system anti-fraud allows you to limit the damage; the system will block calls outgoing (or only international ones) when the consumption threshold will be been exceeded.
6. **Use Fail2Ban (if you PBX is a linux based machine)**  
Fail2ban is a security tool written in Python. It is meant for prevent brute force attacks.
Scan log files and block IP addresses that have too many password failures or use of exploits. (source Wikipedia)
It is able to read system and PBX service log files and blacklist IP addresses responsible for login attempts repeated.

---

Here a complete representation of a VoIP call from caller to callee

![SIP_basic-call&concepts-CALL.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619362246448/Mhrsp-hIy.png)

---

That’s all for this episode too.
Let me know what you think about it.

Thanks a lot for reading, sharing and comment this article.

*Thanks to  [undraw.co](http://undraw.co)* for the beautiful illustration I used in the header  

Cheers!
To be continued in "* [part #5: the main tools for working with VoIP](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-5-the-main-tools-for-working-with-voip) *" …