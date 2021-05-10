## VoIP: fundamentals of SIP and related topics - part #1 basic concepts of SIP

Here we are with the **second** appointment with the mini-series about VoIP.  
You can find the **pilot** here:

%[https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-pilot-0]

---
In this episode I’ll talk about the basic concepts of SIP:
- **Session Initiation Protocol**
- **Transaction, dialog e session**
- **Request e response**
- **B2BUA, server e proxy SIP**
---
## SIP: Session Initiation Protocol
SIP is an application layer protocol; it can establish, modify and terminate a multimedia session, such as a VoIP call.
SIP mainly uses **5 features** in managing a communication session:
- **user location**: to determine the location of the user to communicate with
- **user availability**: to determine the willingness of the called user to participate in the communication
- **user capabilities**: to determine the parameters to be used for the media stream
- **session setup**: to establish the session between two or more users
- **session management**: to manage transfers, modification of session parameters and session termination.  

However, SIP is not a complete communication system, it is only one of the components to be used for complete communication.
SIP is usually used together with the **SDP protocol** (for the description of the multimedia session) and **RTP** (for the real-time transport of the multimedia streaming). We will discuss about it in part #3: SDP & RTP article.

![1_db3E8pOwRPz_0oScL1dlvg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619348672955/h4Jjlr9t0.png)

The main **4 stages** of a SIP session are:
- **location**: search for the location of an endpoint
- **signal**: signaling the willingness to communicate with it
- **negotiation**: negotiation of session parameters
- **teardown**: session closure

The **registration** of the VoIP account (done through the **REGISTER** packets) is the way in which SIP servers can acquire the *location information* (IP addresses, ports and contacts) of their users; during registration, the location server receives the REGISTERS from its users and saves (usually in a database) the association of IP address: port and contact, through a binding operation.
This data will be useful to the SIP server when it has to contact the user to route a direct call to him (*location* stage).  

In SIP, therefore, registration is used for the routing of **incoming calls** while it has no role in the authorization of outgoing calls, which is done with the Proxy-Authorization mechanism inserted within the INVITE.

---

## Transaction, dialog e session
**SIP** is a **transactional** protocol based on a **REQUEST / RESPONSE** model, similar to the HTTP model.
Each SIP transaction consists of a single REQUEST and at least one RESPONSE (0 or more provisional and one or more final responses) to it.
The transaction will be destroyed after sending the final response.

A **SIP dialog** is a peer-to-peer relationship between two UA (user agents) that persists for a certain time and it’s a series of transactions between this two SIP peers.
The **INVITE** is the only SIP packet that can establish a dialog and the response can be either 2xx or 101–199 with To tag (a failure response does not establish a dialog).
A **SIP dialog** is uniquely identified by **From-tag**, **To-tag** and **Call-id**.

A **SIP session** is a collection of participants and media streams between them; it is participated by clients (such as SIP phones, softphones, etc) and by servers (such as SIP registrar, SIP location, SIP proxy).

![1_r93WkyZUdPeagPCX5CM5zA.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619349079623/huGnHDVIr.png)

---

## Request e response
A SIP **request** is identifiable by the **Request-Line** as the first line of packet. The Request-Line contains the **method name**, the **Request-URI** and the protocol version.

```
INVITE sip: 0123456@192.0.2.124 SIP / 2.0
``` 

The main methods are 6:
- **REGISTER**: is used by the UA to indicate its physical location to a SIP registrar server
- **INVITE**: is used to start a session (or to update one in progress)
- **ACK**: is used to confirm that a final response to an INVITE has been received
- **CANCEL**: is used to terminate a pending INVITE, which did not receive a final response
- **BYE**: is used to end the session
- **OPTIONS**: is used to make a request to servers about their capacity other common methods are SUBSCRIBE, NOTIFY, PUBLISH, INFO.

A SIP **response** is identifiable by the **Status-Line** as the first line of packet. The Status-Line contains the protocol version followed by the **Status-Code** in numerical form, followed by the reason phrasees
```
SIP / 2.0 200 OK
```

![1_KTntSSwTouyxebCu1W6iTg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619349228545/NahqYS3l5.png)

The are 6 types of **responses** (divided in 2 macro categories):
**PROVISIONAL RESPONSE**
- **1xx provisional response**: show the receipt of the request, its processing and the status of the server

**FINAL RESPONSE** (indicate to the UAC the final response of the UAS, resulting from the processing of the REQUEST)
- **2xx success**: indicate the positive reception, understanding and acceptance of the request
- **3xx redirection**: indicate that further actions are required to complete the request
- **4xx client error**: tell that the request contains incorrect syntax or that it cannot be satisfied by this server
- **5xx server error**: tell that this server cannot fulfill an apparent valid request
- **6xx global error**: tell that the request cannot be satisfied by any server

---

## B2BUA, server e proxy SIP
A **B2BUA** (back to back user agent) is a logical entity that receives SIP requests and processes them as if it were a UAS (user agent server), therefore as if it were the ultimate recipient of the request.
To know how to respond to the request, a B2BUA also acts simultaneously as a UAC (user agent client) and generates new requests; it will then manage a dialog with the UAC from which the requests arrive and a dialog with the final UAS that must receive them.
A **B2BUA** maintains the status of the dialog and will have to participate in every request of that dialog.
**Why use a B2BUA**: it is used when you need to have full control of the requests arriving on your SIP server in order to build on it the business logic (*for example a billing system*).

A **SIP server** is a network element that receives SIP requests and sends replies.
Some examples of SIP servers: proxy, UAS, registrar server.

A **SIP proxy** is an intermediary entity that acts as a server and client with the purpose of getting requests to clients.
The tasks of a proxy are:
- **routing management**: its job is to find the way to get the request to the right client
- **policy compliance management**: for example checking that a user can make the call

---

That’s all for this episode too.  
Let me know what you think about it.

Thanks a lot for reading, sharing and comment this article.

*Thanks to  [undraw.co](http://undraw.co)* for the beautiful illustration I used in the header  

Cheers!  
To be continued in " [part #2: the basic SIP headers](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-2-the-basic-sip-headers) " …