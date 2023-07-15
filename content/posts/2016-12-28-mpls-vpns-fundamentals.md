---
title: MPLS VPNs – Fundamentals
author: admin
type: post
date: 2016-12-28T23:44:44+00:00
url: /2016/12/28/mpls-vpns-fundamentals/
featured_image: /wp-content/uploads/2016/12/label-maker-with-label-it-tape.jpg
publicize_twitter_user:
  - thecontrolplane
  - thecontrolplane
  - thecontrolplane
publicize_linkedin_url:
  - 'https://www.linkedin.com/updates?discuss=&scope=100787916&stype=M&topic=6220460553593249792&type=U&a=vRh2'
  - 'https://www.linkedin.com/updates?discuss=&scope=100787916&stype=M&topic=6220460553593249792&type=U&a=vRh2'
  - 'https://www.linkedin.com/updates?discuss=&scope=100787916&stype=M&topic=6220460553593249792&type=U&a=vRh2'
categories:
  - MPLS
tags:
  - CCIE
  - L3VPN
  - Networking
  - VPN

---
Ok&#8230; I have a confession to make. &nbsp;I&#8217;ve worked for an MPLS L3VPN provider for about 5 years now. &nbsp;For most of that time, I did not have a real grasp on what MPLS was or how it really worked. &nbsp;When I first got to digging around and working tickets I basically just thought to myself &#8220;oh, it&#8217;s just all different VRF&#8217;s. &nbsp;The VRF&#8217;s separate all the routing tables for each customer. &nbsp;That&#8217;s MPLS.&#8221; &nbsp;I undersold it quite a bit. &nbsp;But that&#8217;s the thing&#8230;. &nbsp;I got by with knowing just that. &nbsp;I guess that&#8217;s a wonderful thing about an MPLS backbone. &nbsp;You configure it once and for the most part, it just works! &nbsp;I want to go over a few of the fundamentals of MPLS and how they specifically apply to MPLS VPN&#8217;s.

### **Purpose/General Concepts of MPLS:** {.wp-block-heading}

The good description I&#8217;ve heard about MPLS is that it&#8217;s &#8220;a way to achieve Layer 3 routing at Layer 2 speeds.&#8221;&nbsp; &nbsp;**Update: I&#8217;ve realized with modern hardware, this statement is no longer relevant 😉**&nbsp;&nbsp;MPLS is essentially a different mechanism that can determine how routers actually forwards packets. &nbsp;Instead of doing a deep inspection of the IP header, MPLS routers can make all &nbsp;or most forwarding decisions based on a 4 byte header that is injected between the L2 and L3 headers. &nbsp;This is known as an MPLS header. &nbsp;There are instances where you will have more than one MPLS label encapsulating the L3 header, but we will get to that later.

CEF plays a big part in MPLS. &nbsp;After a router has decided the best routes to put into its routing table (RIB), it then takes that a step forward with CEF and puts those into its FIB. &nbsp;MPLS goes even one step beyond that and keeps track of all the label bindings for those destination prefixes and places them into something called the LFIB.

### **LDP:** {.wp-block-heading}

Label Distribution Protocol is the primary way that routers can exchange the labels they have allocated for the destination prefixes. &nbsp;LDP uses unicast over TCP port 646 to form adjacencies. &nbsp;Routers that apply, remove, or change MPLS labels are referred to as LSR&#8217;s. &nbsp;This is often referred to as &#8220;push, pop, and swap.&#8221; &nbsp;By default, routers will automatically generate and advertise an MPLS label for each prefix in their routing table to be advertised via LDP when an adjacency is formed. &nbsp;When the routers are advertising their local generated labels, they are essentially telling the other routers &#8220;if you have packets destined for a specific prefix, send the packets to me using this label.&#8221; &nbsp;This will occur across an entire LSP or Label Switched Path.

An Edge&nbsp;LSR is a router that handles both labeled and unlabeled packets. &nbsp; There are times that these routers will not want traffic to come in with a particular MPLS label. &nbsp;For example, if a router were to receive a packet with an MPLS label and it knew that the packet would be forwarded out an interface that is not enabled for MPLS, it would have to inspect the L3 header for more information anyway. &nbsp;Therefore it would prefer to receive the packet as unlabeled to reduce uneccessary&nbsp;processing time. &nbsp;In order for the router to inform other routers to do so, it will send over an MPLS label of 3 for those prefixes. &nbsp;MPLS label 3 is reserved and known as the &#8220;implicit null&#8221; label. &nbsp;You will also hear this referred to as **Penultimate Hop Popping.**

### **Label Stack:** {.wp-block-heading}

As I said before, there will be times when multiple MPLS labels are applied to a single packet. &nbsp;This is referred to as the MPLS Label Stack and is very important when it comes to MPLS VPNs. &nbsp;Depending on the situation, the router will most likely make it&#8217;s forwarding decision based on the outermost label, or the one at the &#8220;top of the stack.&#8221; &nbsp;The router will know if the Label is the &#8220;bottom of the stack&#8221; if it has its S bit set to 1. &nbsp;This lets the router know that the next thing to follow will be the Layer 3 header.<figure class="wp-block-image">

<img decoding="async" loading="lazy" width="717" height="205" src="http://localhost/wp-content/uploads/2016/12/label-stack.png" alt="label-stack" class="wp-image-90" srcset="http://localhost/wp-content/uploads/2016/12/label-stack.png 717w, http://localhost/wp-content/uploads/2016/12/label-stack-300x86.png 300w" sizes="(max-width: 717px) 100vw, 717px" /> </figure> 

##  {.wp-block-heading}

##  {.wp-block-heading}

## **MPLS VPNs:** {.wp-block-heading}

So why would you need multiple labels? &nbsp;The MPLS label stack is a critical piece of traffic forwarding for MPLS VPNs. &nbsp; Pairing MPLS with other technologies such as VRF&#8217;s and Multi-Protocol BGP is a powerful tool for SP&#8217;s to deliver WAN services to customers. &nbsp;Let&#8217;s go over these topics and how they can work with MPLS.

### **VRF:** {.wp-block-heading}

In order to adhere to to the &#8220;VPN&#8221; part, there needs to be some kind of segmentation between customers. &nbsp;This is where you will use a VRF, or Virtual Routing and Forwarding instance. &nbsp;When you create a VRF the router creates a new virtual instance of the routing table. &nbsp;In addition, only interfaces that are specifically assigned to that particular VRF are belong in that specific routing table. &nbsp;What you have created it essentially a VPN. &nbsp;The VRF contains it&#8217;s on control plane instances as well as the data plane is separated based on routing. &nbsp;One of the most important things here is that you can have overlapping address space between VRF&#8217;s since they are all within their own virtual routing table!

Now that you have interfaces segmented into different VRF&#8217;s, how do you scale this? &nbsp;If you&#8217;re a glutton for punishment and have a large network, you can do something referred to as &#8220;VRF Lite.&#8221;

<div class="wp-block-image">
  <figure class="aligncenter"><img decoding="async" loading="lazy" width="471" height="303" src="http://localhost/wp-content/uploads/2016/12/vrf-simple.png" alt="vrf-simple" class="wp-image-185" srcset="http://localhost/wp-content/uploads/2016/12/vrf-simple.png 471w, http://localhost/wp-content/uploads/2016/12/vrf-simple-300x193.png 300w" sizes="(max-width: 471px) 100vw, 471px" /></figure>
</div>

### **VRF Lite:** {.wp-block-heading}

VRF Lite is essentially the barebones approach to using VRF&#8217;s. &nbsp;In order to maintain traffic segmentation through the network, VRF&#8217;s must be locally created on every device in the path if you want correct functionality. &nbsp;This is obviously not the preferred approach because of its issues with scalability and overhead.

In order to efficiently exchange routes, but still keep them within the VRF&#8217;s you have defined, you can use Multi-Protocol BGP.

### MP-BGP: {.wp-block-heading}

Multi-Protocol BGP is a number of extensions built on top of BGP that allow you to do more than just normal exchange of NLRI&#8217;s.

Let&#8217;s take a look at one of the issues you will obviously see with our previous example of multiple VRF&#8217;s on a single router: overlapping address space. &nbsp;Normal behavior of BGP consists of only advertising the BEST route for a specific prefix to its neighbors. &nbsp;If you have the same prefix in multiple routing tables&#8230; how does the router know which one to advertise? &nbsp;You overcome this issue by defining something with the VRF known as the Route-Distinguisher.

#### Route-Distinguisher: {.wp-block-heading}

The purpose of the Route-Distinguisher is to do just that&#8230; DISTINGUISH the route. &nbsp;It is an 8 byte value that is added to the beginning of the NLRI prefix. &nbsp;It must be unique per VRF. &nbsp;The format is two values, separated by a colon. &nbsp;Often, the format you see used is below:

  * ASN:nn
  * IP-addres:nn

However, this value is truly arbitrary and can be whatever you&#8217;d like it to be.

The combination of an NLRI prefix and it&#8217;s RD is known as a VPNv4 route. &nbsp;This is what gives the router the ability to advertise the same prefix multiple times to its BGP neighbors. &nbsp;Each VPNv4 route is truly unique. &nbsp;For example, if multiple customer are using the 10.1.1.0/24 prefix, here is how the router will advertise each of the unique VPNv4 routes:

<div class="wp-block-image">
  <figure class="aligncenter"><img decoding="async" loading="lazy" width="601" height="425" src="http://localhost/wp-content/uploads/2016/12/vpnv4-route-advertisement.png" alt="vpnv4-route-advertisement" class="wp-image-188" srcset="http://localhost/wp-content/uploads/2016/12/vpnv4-route-advertisement.png 601w, http://localhost/wp-content/uploads/2016/12/vpnv4-route-advertisement-300x212.png 300w" sizes="(max-width: 601px) 100vw, 601px" /></figure>
</div>

Note: The RD only makes the route unique so that it can advertise each prefix to its neighbors. &nbsp;The RD does **NOT** determine how the receiving router will inject those routes into the corresponding VRFs. &nbsp;That is actually done via&nbsp;the route-target.

#### Route-Target: {.wp-block-heading}

The route-target is a specific 64 bit extended community value that is applied to each NLRI in BGP VPNv4 route. &nbsp;This is how the VRF&#8217;s both IMPORT and EXPORT routes. &nbsp;The RT is an 8 byte field that follows the same format as the RD (ASN:nn or IP-address:nn). &nbsp;The VPNv4 speaking router actually only accepts VPNv4 routes that have a local VRF importing that specific route-target. &nbsp;Route reflectors are the exception to this rule, as they are responsible for sending all VPNv4 routes to all routers within that AS.

You can define specifically within each VRF what route-targets they would like to import and export. &nbsp;They can have multiple import and export statements if necessary. &nbsp;You can also apply filters to these imports and exports if necessary via a route-map.

<ul class="wp-block-gallery columns-3 is-cropped wp-block-gallery-21 is-layout-flex">
  <li class="blocks-gallery-item">
    <figure><img decoding="async" loading="lazy" width="492" height="216" src="http://localhost/wp-content/uploads/2016/12/screen-shot-2016-12-29-at-8-11-06-pm.png" alt="" data-id="191" class="wp-image-191" srcset="http://localhost/wp-content/uploads/2016/12/screen-shot-2016-12-29-at-8-11-06-pm.png 492w, http://localhost/wp-content/uploads/2016/12/screen-shot-2016-12-29-at-8-11-06-pm-300x132.png 300w" sizes="(max-width: 492px) 100vw, 492px" /></figure>
  </li>
  <li class="blocks-gallery-item">
    <figure><img decoding="async" src="http://localhost/wp-content/uploads/2016/12/screen-shot-2016-12-29-at-8-11-30-pm.png" alt="" data-id="192" class="wp-image-192" /></figure>
  </li>
</ul>

#### Capabilities Exchange: {.wp-block-heading}

As with normal behavior of BGP, routers must exchange capabilities in the OPEN message to determine what each BGP speaker supports. &nbsp;Normally, IPv4 route exchange is enabled by default (you can disable this behavior, which is necessary in some cases). &nbsp;In order for routers to exchange VPNv4 routes, or IPv6/VPNv6 routes for that matter, that specific neighbor must be enabled to do so under the appropriate address-family in BGP. &nbsp;Both routers must be in agreement on their capabilities for them to send/receive these specific routes.

#### Provider/Client Edges: {.wp-block-heading}

When delivering an L3VPN for a customer, every router plays a specific role on the end to end path. &nbsp;There are P Routers, PE Routers, and CE Routers.

  * PE Routers 
      * Provider Edge
      * Need to be VRF aware
      * Import/Export routes in and out of specific VRFs
      * Peer MP-BGP with other PE routers
      * Peer BGP or IGP with CE routers
  * CE Routers 
      * Client Edge
      * Do not need to be VRF aware, but can be in some cases
      * Responsible for taking in customer routes and sending them to PE routers
  * P Routers 
      * Provider Routers
      * These are what will be seen as your &#8220;backbone&#8221; MPLS routers. &nbsp;Speed is key here.
      * Not VRF aware, do not even need to run BGP
      * Only need routes for loopbacks of PE routers that are peering MP-BGP 
          * Can be done with an IGP like EIGRP or OSPF

<div class="wp-block-image">
  <figure class="aligncenter"><img decoding="async" loading="lazy" width="852" height="412" src="http://localhost/wp-content/uploads/2016/12/routerroles.png" alt="routerroles" class="wp-image-201" srcset="http://localhost/wp-content/uploads/2016/12/routerroles.png 852w, http://localhost/wp-content/uploads/2016/12/routerroles-300x145.png 300w, http://localhost/wp-content/uploads/2016/12/routerroles-768x371.png 768w" sizes="(max-width: 852px) 100vw, 852px" /></figure>
</div>

###  {.wp-block-heading}

###  {.wp-block-heading}

### Where does MPLS fit into this? {.wp-block-heading}

All the topics I just went over with respect to MPLS VPNs all had to do with route-exchange. &nbsp;So how is the traffic actually forwarded? &nbsp;Why do we need MPLS to form the VPN?

The need for labels comes in when you further dissect the logic that the router will use to forward the traffic. &nbsp;Sure, you may have set up all the routers to exchange the appropriate VPN routes, but you did those across GLOBAL interfaces (or at least interfaces that are not associated with the &#8220;customer&#8221; VRFs). &nbsp;If a packet were to come in on an interface that is no associated with a VRF, why would that router ever consult the VRF&#8217;s specific routing table? &nbsp;How would it know to do so?

I previously mentioned the fact that you may have more than one MPLS label on a particular IP packet. &nbsp;The concept of the MPLS label stack is not only how the router forwards VRF traffic across a global interface, but it also let&#8217;s the receiving router know which VRF the traffic is destined for. &nbsp;I&#8217;ve heard these labels given a few different terms, but I&#8217;ll refer to them as the Transport Label and the MPLS Label.

  * Transport Label: 
      * Generated by LDP across MPLS backbone
      * Outermost label or top of label stack
      * Used to forward traffic between PE routers
  * MPLS Label: 
      * Generated by BGP
      * Innermost label, or bottom of stack
      * Used to get traffic into the appropriate VRF

So&#8230; when a router is forwarding traffic that is within an MPLS VPN, it must perform 2 label lookups, one for the MPLS label and one for the Transport label. &nbsp;Let&#8217;s look a the traffic flow:

  1. A packet comes in on a VRF enabled interface. &nbsp;The destination address in the IP header is inspected and compared to that VRF&#8217;s routing table for the best match.
  2. A match is found in a route learned from BGP. &nbsp;This route is flagged that MPLS is required and includes the MPLS label that was learned via BGP from the other PE router. &nbsp;**This is your MPLS label**.
  3. Now, the router needs to find the label to use for the next hop address, which is another PE router. &nbsp;The router will then consult the MPLS forwarding table, or LFIB to get the label for the next hop router. &nbsp;**This is your Transport label.**
  4. The labels are stacked onto the packet and encapsulated in the L2 frame to be forwarded to the P router.
  5. The P routers label switch based on the outermost label, or the&nbsp;**Transport** label. &nbsp;Before the last P router sends the traffic to the appropriate PE router, it pops off the transport label, leaving only the MPLS label. &nbsp;As we discussed before, the PE will not need the transport label, because it is the router that generated it.
  6. The PE router receives the packet and sees only the MPLS label. &nbsp;The router refers to it&#8217;s LFIB and see that this particular label is an aggregate label and to consult the RIB for that particular VRF to make it&#8217;s next forwarding decision.<figure class="wp-block-image">

<img decoding="async" loading="lazy" width="717" height="307" src="http://localhost/wp-content/uploads/2016/12/label-stack2.png" alt="label-stack2" class="wp-image-225" srcset="http://localhost/wp-content/uploads/2016/12/label-stack2.png 717w, http://localhost/wp-content/uploads/2016/12/label-stack2-300x128.png 300w" sizes="(max-width: 717px) 100vw, 717px" /> </figure> 

As I said, MPLS VPN&#8217;s are a powerful tool in a Service Provider toolset in order to provide scalable WAN connectivity for multiple customers. &nbsp;There are a number of other technologies and caveats that come into play with MPLS VPN&#8217;s, but this post was just to cover some of the fundamentals.