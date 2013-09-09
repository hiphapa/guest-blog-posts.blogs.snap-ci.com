---
layout: post
title:  Announcing Support for Some NoSQL Databases
date:   2013-09-07
author: Ketan Padegaonkar
categories: announcements features
---

Today we're adding support for 3 of the most requested NoSQL databases. For the last few months, these popular databases have been some of the most common requests from customers.

To that end we are making CouchDB, MongoDB and Redis generally available for all builds. You may access these databases over on their default ports. We have disabled authentication, so your builds should be able to connect to them unauthenticted. As part of our "builds should start from a clean slate" philosophy these databases will be cleaned out before every build.

Please [contact us]({{ site.link.contact_us }}) if there's a feature you'd like on Snap.