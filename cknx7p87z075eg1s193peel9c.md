## VoIP: fundamentals of SIP and related topics - part #2: the basic SIP headers

Here we are with the **third** appointment with the mini-series about VoIP.  
You can find the **last** here:

%[https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-1-basic-concepts-of-sip]

---

In this episode I’ll talk about the **basic SIP headers**:

1. Request-URI
2. From & To
3. Call-ID
4. Via & Contact
5. Record-route & Route
6. P-Asserted-Identity Remote-Party-ID e P-Preferred-Identity

---

## Request-URI

The **Request-URI** indicates the URI (the address) of the UAS to which the packet is to be sent.

The initial **Request-URI** (of an INVITE not in dialog) must be equal to the URI present in the To field (except for REGISTERS).

How to write a R-URI? The **Request-URI** of requests in dialog must follow this rule:
- if **there are NO** *Record-Route* header, the **Request-URI** must be equal to the URI present in the **Contact** of the response.
- if **there are** *Record-Route* headers:
  1. if the first URI of the Record-Route headers contains the parameter **lr**, then the UAC must use the **Contact** of the response to populate the Request-URI (plus it must add the routes)
  2. if the first URI of the Record-Route headers does NOT contain the **lr** parameter, then the UAC must use the first Record-Route header of the response to populate the Request-URI

---

## From & To

The **From**

```
From: “0123456” <sip: 0123456@192.0.2.123>; tag = 1234
```
This header indicates the logical entity from which the request originates; usually it is the AOR (address-of-record) of the user.  
It contains a SIP or SIPS URI and can contain the display name (“0123456” in our example).
This field is used by the SIP proxy that receives the request, to determine which “processing rule” to apply to this request (for example, that user may not be enabled for calls).

The **To**

```
To: “0123456” <sip: 0123456@192.0.2.124>; tag = 1234
```
This header indicates the logical entity you want to contact, the AOR of the target user of the request.  
It contains a SIP or SIPS URI and can contain the display name (“0123456” in our example).

---

## Call-ID

```
Call-ID: f88f9s8f8fsd898332-fff23fsf-23@192.0.2.234
```
It is used as a globally unique identifier to group a series of SIP messages.  
In all requests and responses exchanged by UA in the same dialog, **it must always be the same**.
It should be the same in all registration requests from a UA.
Combined with the from-tag and the to-tag, it uniquely identify a dialog.

---

## Via & Contact

The **Via** (present in a request) identifies the address at which the person who generated the packet expects to receive the response.  
The Contact contains a SIP or SIPS URI and represents a direct way to contact whoever generated the packet.

> So the Via says where to send the answers; the contact instead indicates where to send future requests in the same dialog.  
To find out where to send a 200OK (which is a response) I will use the address present in the Via, while to send a BYE (which is a request) I will use the address present in the Contact.

Via works with the **breadcrumbs mechanism**: a new Via is added to the head from each SIP element that packet passes through and the previous hops are left.

---

## Record-route & Route

Only proxies that use the ** [Loose Routing](https://www.rfc-editor.org/rfc/rfc3261.html#section-16.12) ** mechanism will add record-routes to the SIP packet.  
This mechanism allows a proxy to continue to be “visited” (engaged) for the duration of the dialog.  
The **loose routing proxy** adds the record-route header to a SIP request to force future requests in the thick dialog to always pass through it.  
Route headers are used to force the routing of requests through to a list of proxies.

> With the Record-routes the path is memorized and with the Routes the routing is managed properly.

Record-routes and routes also work with the **breadcrumbs mechanism**.

![route.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619357285816/1r6dhA0jt.png)
*Thanks to  [https://docplayer.it/2339191-Sip-session-initiation-protocol.html](https://docplayer.it/2339191-Sip-session-initiation-protocol.html)*

---

## P-Asserted-Identity Remote-Party-ID e P-Preferred-Identity

The **P-Asserted-Identity** is used to communicate that the identity of the caller has occurred (for example using Digest Authentication, SSO with token, etc) and it is the one indicated in the P-A-I header.  
The recipient will be able to use this information instead of providing authentication, if he considers the generator of that header an entity to be trusted, then he will be able to use that information instead of providing authentication.  
If an entity (e.g. a carrier) receives a P-A-I from an entity it does not know (eg not you, someone on the internet), then it will not consider that identity proven, but will use some form of authentication (or simply ignore the call).

The **Remote-PID** is the old header that was used before in place of the P-A-I, now obsolete (albeit used).

The **P-Preferred-Identity** is an indication by a User Agent on which identity a receiving proxy that header should use in the P-A-I.  
The proxy may decide to use that identity (for example if more than one can be applied), put it in P-A-I, and remove P-P-I from the outgoing INVITE.

---


![sngrep.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619357448701/LoX6V3EKK.png)

This is an INVITE packet. All the green one is an header; the most used we have described above.  
In the lower part there is the description of the SDP. We will address this topic in the next episode.

---

That’s all for this episode too.  
Let me know what you think about it.

Thanks a lot for reading, sharing and comment this article.

*Thanks to  [undraw.co](http://undraw.co)* for the beautiful illustration I used in the header  

Cheers!  
To be continued in " [part #3: SDP & RTP](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-3-sdp-and-rtp) " …