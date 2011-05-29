---
kind: article
layout: post
date: 2009-07-30 19:23:00 -04:00
title: Wildcard App IDs and Push Notifications
existing_url: http://mrox.net/blog/2009/07/30/wildcard-app-id-and-push-notifications/
---
Back in the Dark Ages of iPhone Development, when the NDA loomed over us all, getting information was hard.  The deployment and code-signing piece was especially tricky to get working correctly.  One was often thrilled to get it working at all.

One step of the process is choosing an application ID, which uses the common reverse domain name format.  The documentation also stated that if you want to share information across multiple applications, a wildcard app ID (com.yourcompany.\*) can be used.  No drawbacks were given, and sharing is good, right?  So I can only imagine that some people may have used a wildcard app ID for their application.  And I can also only imagine that these people had no problems with their wildcard app ID, until iPhone OS 3.0 came out that is.

You see, Apple's Push Notification service [using a wildcard application ID](http://developer.apple.com/IPhone/library/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ProvisioningDevelopment/ProvisioningDevelopment.html).

Those same people may have been under the impression that it was the app ID that uniquely identified the application, and therefore changing it would yield a *new* application, and not update the existing one.  This means that all ranking in the App Store would be lost, and users would not be notified of the update.

The good news is, this is not the case.  An application's app ID *can* be changed by generating a new app ID, re-generating a distribution the App Store distribution certificate, and submitting to the App Store.

So if you shipped your app with a wildcard ID, and now want to integrate push notifications, you are not screwed.  Just generate a new, non-wildcard app ID.