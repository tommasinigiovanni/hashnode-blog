## VoIP: fundamentals of SIP and related topics - pilot #0

**VoIP** stands for **V**oice **o**ver **IP**. **VoIP** refers to communications that no longer take place through a traditional telephone network, but through an IP network.

More specifically, **VoIP** refers to the set of application layer communication protocols that make this type of communication possible.  
The **information** (both the signaling to establish, manage and close the call, and the voice), instead of traveling on an analog telephone line, crosses any network based on an IP protocol.  
The communication that the **VoIP** system allows to obtain is of the **real-time audio-video**, unicast or multicast type (therefore video calls and videoconferences, for example), and allows you to easily reach any type of user.   
It therefore offers the possibility to make and receive calls from other VoIP numbers, analogue numbers, national numbers, international numbers, fixed and mobile users, all toll-free numbers and emergency numbers.

 [Here](https://medium.com/r/?url=https%3A%2F%2Fgetvoip.com%2Fblog%2F2014%2F01%2F27%2Fhistory-of-voip-and-internet-telephones%2F) , thanks to  [getvoip.com](https://medium.com/r/?url=http%3A%2F%2Fgetvoip.com) , you can find a nice and complete article on the birth and evolution of the VoIP, from 1928 to today.

In the last years the development of **VoIP** has been very fast and its technology is constantly evolving; all of this was possible thanks to the widespread diffusion of high performance and broadband internet connections.

For **data transport** (the voice), in the vast majority of **VoIP** implementations, a set of communication protocols such as **RTP** (Real-time Transport Protocol) over **UDP** (User Datagram Protocol) over **IP** (Internet Protocol) are adopted. The use of UDP instead of TCP is justified by the fact that a real-time communication such as voice cannot tolerate additional or too high delays or latencies due to the retransmission of lost packets.  
For the **signaling** (that is everything that concerns the reaching of the user, the establishment of the call, the maintenance of communication and then the closure of the call) there is a vast dominance of the use of the **SIP protocol**, over both **UDP** and **TCP**.

---

The web is full of article and video about this topic.  
One of the most viewed YouTube video is this one
 
%[https://www.youtube.com/watch?v=2x3Ie6VZ_sg]

---

In this *mini-series of articles* I want to give my contribution on this topic. 
The series will be divided into the following parts:

- pilot #0: *(the article you are reading) a brief introduction to the mini-series*
-  [part #1: *basic concepts of SIP*](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-1-basic-concepts-of-sip) 
-  [part #2: *the basic SIP headers*](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-2-the-basic-sip-headers) 
-  [part #3: *SDP & RTP*](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-3-sdp-and-rtp) 
-  [part #4: *UA PBX, standard configurations and some security tips*](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-4-ua-pbx-standard-configurations) 
-  [part #5: *the main tools for working with VoIP*](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-5-the-main-tools-for-working-with-voip) 
- [part #6: audio problems and the quality of a VoIP call](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-6-audio-problems-and-the-quality-of-a-voip-call)
- season finale #7: *final conclusions *

Let me know if this is an interest topic to you and if you like the lineup or if there are any topics / insights that you would like to speak about.  

> 
Writing about a topic is a way to "understand if I understand", to collect contributions (I hope there will be many comments that will confirm what I have written and many comments that will correct me) and to help the sharing of knowledge and I love all of this stuff!

---


Thanks a lot for reading, sharing and comment this article.

*Thanks to  [undraw.co](http://undraw.co)* for the beautiful illustration I used in the header

Cheers!

