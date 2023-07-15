---
title: CCIE Strategy – Config Section
author: admin
type: post
date: 2019-06-21T03:26:33+00:00
url: /2019/06/21/ccie-strategy-config-section/
featured_image: /wp-content/uploads/2019/06/image-3.png
timeline_notification:
  - 1561087598
  - 1561087598
  - 1561087598
publicize_twitter_user:
  - thecontrolplane
  - thecontrolplane
  - thecontrolplane
publicize_linkedin_url:
  - 
  - 
  - 
categories:
  - Uncategorized
tags:
  - CCIE

---
This post will detail a strategy that I personally used during my recent attempt(s) at the CCIE R&S lab exam, specifically on the configuration section. This was a strategy that a few of my study group members and I curated during our time studying for the lab together. I&#8217;ve spoken to some others who followed a very similar approach, just with a few tweaks.  
  
\*\\*\*CURVEBALL FROM CISCO\*\**  
  
So… Cisco decided to drop a major update to their certification program a few weeks ago at CLUS, shortly after I started drafting this blog post. That being said, this post is focused on v5 of the R&S lab. Therefore, it will have a shelf life of about 8 months until the test changes in February of 2020. Some of the content may still be applicable afterward, but since the entire test format changed, I won&#8217;t bank on it. 😉  


#### Assumptions: {.wp-block-heading}

In this post, I&#8217;ll be assuming the reader is familiar with the format of the CCIE lab. Specifically the format and content of the CONFIG portion. 

#### Credits {.wp-block-heading}

Throughout this post I will be utilizing a lab that was actually created by a good buddy of mine, Tim McConnaughy. Tim created this lab as a means to help people studying for the lab to have some extra practice material after he passed the lab. He&#8217;s done a great job contributing back to the community that helped him and I thank him for letting me use this material. He presented at CLUS 2019 in San Diego and has a great blog. Check out his content and harass him into making more labs like this 😉

<https://carpe-dmvpn.com>

<https://bit.ly/2Y0ivHh> : Introduction to IP Multicast &#8211; BRKIPM-1261

## Why is a strategy important? {.wp-block-heading}

First of all, I want to stress that everyone is different and it is important to find out what works for you. There are people out there who could probably walk in, start at task 1, go to town, and walk out with a fresh set of digits. That didn&#8217;t work for me. One of my prominent struggles in the lab exam centered mainly around reading comprehension and retention. So I needed a little more structure to my approach in order to conquer the lab. My buddy, Henry Pardo, and I used techniques from others and created a quick strategy for us that really helped us save time and effort while in the lab. This strategy helped us put everything in a very particular order on how to approach the lab. This kept me from having to jump around, wondering what to do next, and having to go back and reconfigure items. I was able to have it it all in a nice format so I spent even less time scatterbrained.  
  
Ok&#8230; let&#8217;s get to it. 

### Step 1 &#8211; Go ahead and complete the Layer 2 section {.wp-block-heading}

Once you get to start the config section of the lab, I think it&#8217;s best just to keep rolling with your momentum and jump right into configuring the Layer 2 section. Many people will say, &#8220;NO! You should start by reading the entire list of tasks/requirements before doing anything!&#8221; If this were a real-world scenario, I would wholeheartedly agree. But if you understand the virtual topology you&#8217;ve been dealt, you know nothing is going to work until you get the Layer 2 section configured and running first, so you might as well do it (this will make more sense later). By this, I mean go ahead and read the entire Layer 2 section, digest the requirements, and get it done. 

By the time you&#8217;re done with this, it will probably be a little close to lunch time. 

### Step 2 &#8211; Number your sheet {.wp-block-heading}

This is where you&#8217;ll want to grab the scratch paper. Since you&#8217;ve already configured the Layer 2 portion of the lab (Section 1), we&#8217;ll start with Section 2 and so on. This will include Layer 3, VPN Technologies, Security, and Services.  
Without actually reading any of the tasks, just scroll through and see how many tasks are in each section and jot them down in a column on the left side of your paper. For example, if you notice the Layer 3 section has 12 tasks, write down 2.1 all the way through 2.12. You&#8217;ll want to leave a little space on the right-hand side for checking off these items later. In the end, your sheet will look something like this:<figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/img_0179.png" alt="" class="wp-image-333" /> </figure> 

### Step 3 &#8211; Analyze the full topology divide into &#8220;sites.&#8221; {.wp-block-heading}

Like I said before, I&#8217;ll be using my friend Tim&#8217;s lab as an example here. You&#8217;ll then take a look at the full topology diagram they provide you in the documents section. Like so:<figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/image-3.png" alt="" class="wp-image-332" /> </figure> 

Lucky for us, Tim made this pretty easy to see where the site boundaries are. We have a DC, an HQ, offices in Beijing and Shanghai, and some remote worker sites down in the bottom. Also, we have an MPLS provider in the middle connected to some other SPs. We&#8217;re going to basically start putting these sites on our paper in the shape of how they appear on the diagram. We&#8217;ll put a header on each one with the site name, and I also like to go ahead and add the BGP AS on there as well (where applicable). This will come in handy later when you&#8217;re crafting configs and don&#8217;t want to dig through the documents to find the AS number. You can just glance over at your sheet. <figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/img_0180.png" alt="" class="wp-image-334" /> </figure> 

### Step 4 &#8211; Read through each task and assign to the appropriate site. {.wp-block-heading}

This is where you&#8217;ll spend the most time. Read each task, starting at the beginning, and digest what&#8217;s being asked. Then write the task number under the site where it&#8217;s applicable. For example, here&#8217;s the first task of Tim&#8217;s lab:<figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/image-1-1.png" alt="" class="wp-image-330" /> </figure> 

Write this task under the MPLS Core site with some shorthand about what the task is. &#8220;2.1 &#8211; OSPF.&#8221; In the description of the task, you can also add a keyword to help you remember something unique about the task that you don&#8217;t want to forget. If you read a task, later on, that implies you should use named mode when configuring EIGRP instead of classic mode, jot it down on the primary task that tells you to configure EIGRP. Then move along to the next task and continue. 

_Tip_  
As you go, pay attention to which tasks give you tasks for verification. i.e., if a task tells you to &#8220;match this output&#8221; or result in a particular routing behavior, you&#8217;ll want to make a note of that. Any task that requires such a thing put a tiny &#8220;v&#8221; next to the task in the column you made on the left-hand side. By doing so, you&#8217;ll know precisely the tasks to revisit during your verification process.<figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/img_0182.jpeg" alt="" class="wp-image-335" /> </figure> 

You&#8217;ll find that some tasks apply to more than just one site. Let&#8217;s say you get a task that involves configuring eBGP at all sites. You&#8217;ll want to list this task under every single site that needs eBGP configured. Here&#8217;s an example from Tim&#8217;s lab:<figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/image-2.png" alt="" class="wp-image-331" /> </figure> 

This task requires configuration of the HQ, Home User, and CC agent sites. 

**Repeat this as you study.  
** The first time you do this step, it might take a good 30-40 minutes. Don&#8217;t worry, you&#8217;ll get faster. Make this part of your routine, and you&#8217;ll become more efficient. One of the biggest mental hurdles I needed to get over was the fact that reading ≠ wasting time. I knew I was against the clock and wanted to skim as quickly as possible get in the damn CLI! What I found was taking this time to put together your plan of attack kept me from wasting time later on. It was critical.

Once you&#8217;re done with this step, you&#8217;ll have a full sheet that basically outlines where you&#8217;ll go, start to finish. <figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/img_0181-1.png" alt="" class="wp-image-336" /> </figure> 

### Step 5 &#8211; Get in that CLI, baby! {.wp-block-heading}

This is where you get to crack your knuckles and jump in. Start with whichever site you want to do first and get going. After doing this a few times, you &#8216;ll develop a method of determining what the best site to start with based on your requirements is. 

From our example lab, let&#8217;s say you start with the Headquarters location. You&#8217;ll roll through these tasks, directly in order:<figure class="wp-block-image">

<img decoding="async" src="http://localhost/wp-content/uploads/2019/06/img_0183.jpeg" alt="" class="wp-image-337" /> </figure> 

Once you&#8217;re done, you&#8217;ll know you completed all the tasks for HQ, and you move onto the next site. 

**Tasks that encompass multiple sites**  
Let&#8217;s go back to our eBGP example task. Let&#8217;s say you get to the first instance of this task when you&#8217;re configuring everything per-site. Depending on the task, it may be a good idea to go ahead and configure all the devices involved with this particular task. For example, you may only be configuring the HQ, but for this task, you go ahead and configure this item at all the sites. My argument will be only to do this if you can RE-USE config. You can hop in the first device, configure BGP from scratch, and the view the running config and have the exact syntax that you&#8217;ll use everywhere. Just copy, pop it in notepad and replace what variables you need to (i.e., the BGP ASN, neighbor address, etc.). This could potentially save you a few minutes, and then you can actually cross this particular task off for every site instead of just 1. 

**Just keep rolling**  
The goal here is configuration, not verification. Don&#8217;t spend too much time checking little details while moving through this. The goal here is to FINISH all the configuration items. By design, you should have a good chunk of time at the end of the exam, specifically for verification. 

### Step 6 &#8211; Verify and verify again. {.wp-block-heading}

At this point, all the configuration items that you were able to complete are done. Take a look at the left-hand column you made with the list of the tasks and notice all the ones that require verification of some sort. You can go in numerical order, or start with the most important ones. 

Once you&#8217;ve gone through this a few times with separate labs, you&#8217;ll notice that you&#8217;ll start to have more and more time for this step. This is very useful, and you&#8217;ll find a lot of critical items you may have missed along the way. Here&#8217;s where you&#8217;ll match outputs, tweak routing policies, and maybe notice some little &#8220;gotchas&#8221; that you may not have picked up on the first time you read a task. 

## Summary {.wp-block-heading}

I know what you may be thinking. You just read over 1500 words about something somewhat meticulous and stressed on &#8220;saving time.&#8221; It may seem like overkill, but if you&#8217;re a person that has trouble keeping things in order, this may add some coordination to the chaos. Plus… the time is ticking on CCIE RS v5.0, so I think going in with a strict plan of attack can&#8217;t hurt you any more than it can help. 

If you think it will help, work this into your labbing routine. You don&#8217;t necessarily have to do this start to finish in every single session. If you only have a couple hours to kill, start up a lab, and work your way through this. Once you get to a stopping point, you can save your progress and come right back to where you left off the next day. There were even times where I just practiced putting together the plan. I&#8217;d grab a brand new lab (or maybe one I simply hadn&#8217;t done in a couple weeks) and just do steps 2 through 4. As i said, I struggled with reading comprehension. This repetition helped me get to the point where I could have it all done in about 10-15 minutes. 

###  
  
 {.wp-block-heading}