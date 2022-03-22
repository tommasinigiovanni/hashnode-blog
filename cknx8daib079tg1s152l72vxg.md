## VoIP: fundamentals of SIP and related topics - part #3: SDP & RTP

New week and new episode, the fourth appointment with the mini-series about **VoIP**.  
You can find the last here:

%[https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-2-the-basic-sip-headers]

---

In this episode I’ll talk about the **SDP & RTP**:
1. What is **SDP**
2. **Codec**
3. What is **RTP**

---

As I mentioned in the previous articles, **SIP** is only one of the protocol engaged in a VoIP call.  
The **SIP** is the protocol that deals with the exchange of messages (on UDP or TCP) to *establish*, *modify* and *terminate* a session. However, it does not deal with the exchange of the audio stream.

The management and exchange of the audio stream is delegated to 2 other protocols: the **SDP** and the **RTP**.

---

## What is SDP

The **SDP** protocol is used to describe the details of the multimedia session.  
The details of the session can be found in the body of some SIP messages (such as the `INVITE` or `200 OK`).

This is a screenshot of an INVITE take with **Wireshark** (one of the useful tools for debugging the SIP; we will see in detail in one of the next episodes)

![wireshark.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619358150088/lZ98cABTx.png)

As you can see, the SIP, as we already know, it’s composed of: **Request-Line**, **Message Header** (I talked about the most important headers in the last episode) and **Message Body**.

Let’s expand the package:

![wireshark-expand.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619358205833/Z_QcPUu5t.png)

The **Request-Line** (yellow) is composed by the **Method** and the **Request-URI** (the destination of the packet).  
The **Message Header** (green) is made up of all the headers we talked about last episode.  
And the **Message body** (blue) contains the **SDP** informations.

The most important **SDP** informations to know are the **c** and the **m** lines.
In the **Connection Data**, the **c field** there is the **IP address** at which you want to receive the multimedia stream.  
The **Media Descriptions**, the **m** field describes the port at which the stream is to be received and its encoding (the codecs you are allowed to use).

> If you are talking to a SIP switch on the public network and in the c field you put your private IP (instead of your public IP), the call may have one-way audio, because the SIP exchange you want to contact will not know which is your public IP where to send the audio stream; so they will hear you but you don’t hear them.

---

## Codec

Codecs are comparable to languages known to humans. If you have to talk to a person, you have to use a common language, otherwise you will not understand each other. If your mother tongue is different, before starting to converse, you will agree on which language to speak.  
It could happen that you say: "*I know English, French and Spanish and you?*", and your interlocutor replies: "*I know Russian, Slovenian and English, so let’s speak in English*".  
The only language common to you two is *English* and therefore your communication will take place in *English*.    
This is the mechanism that underlies the management of **codecs** in a VoIP communication.

Caller and callee declare in the SDP the supported codecs in order of preference.  
In the presence of several common codecs between the caller and the callee, the first codec chosen by the callee is generally used.  
In any case it is possible to have media streams with any of the supported codecs and they can change during the phone call.  
To undo the declaration of support for a given codec (or to declare that a new one is supported) you need to send a new INVITE (RE-INVITE) with a new SDP.

It could happen that the two interlocutors do not have a language, a codec in common; in this case the communication cannot be established, unless there is a B2BUA in the communication that offers the **Transcoding** service, i.e. it will act as **simultaneous translator**.

Each codec has a numeric code and a name that identifies it.  
The most used voice codecs in Europe are:  
`8 pcma` (or g711-alaw)  
`18 g 729`  
`9 g722`  
`3 gsm`  
`0 pcmu` (or g711-ulaw)  
`13 comfort noise`  
`101 telephone event`  

In the example above, you can see that the caller proposed the following codecs: `8, 0, 101` (Look in the Media Description **m** line).

---

## What is RTP

The **RTP** (**Real-time Transport**) application protocol takes care of sending a media stream to the endpoint.  
The only transport protocol used is **UDP**.  
The port to which the stream is to be received is communicated in the SDP, in the field **m**.


![sngrep-rtp.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619358571196/IbGIiksHu.png)


In this image, taken with another tool (**sngrep**) that we will see in the next episode, you can see the SIP packets (red and green) and the RTP stream (white).

---

That’s all for this episode too.  
Let me know what you think about it.

Thanks a lot for reading, sharing and comment this article.  

*Thanks to  [undraw.co](http://undraw.co)* for the beautiful illustration I used in the header  

Cheers!  
To be continued in " [part #4: UA PBX, standard configurations](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-4-ua-pbx-standard-configurations-and-some-security-tips) " …