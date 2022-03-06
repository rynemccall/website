---
title: Rules for a new software company
date: 2014-05-11T20:19:00-0400
updated:
  - 2015-04-11T00:00Z
  - 2022-03-06T08:00-0500
tags:
  - technology
  - business
---

If you are staring a new company, you have a once-in-the-company's-lifetime opportunity to make some simple policy decisions that will pay enormous dividends if the company makes it. Unfortunately, these policy decisions are so simple and mundane, they are typically made on an ad-hoc basis without much consideration.

Here are a few things that have frustrated me that are incredibly difficult to fix once a company has grown beyond a handful of people.

# Enforce the use of a company wide chat tool

[Hipchat](https://www.hipchat.com/) or [Campfire](https://campfirenow.com/) are fine options. [Skype](http://www.skype.com/) and IRC are not.

There are only a few things the chat tool needs to be able to do:

* must support 1 to 1 chats
* must support ad-hoc and long-lived group chats that are easily discoverable
* must have a rich API
* must be usable by a non-technical person without any technical support

# Make all information searchable

All information storage systems must interface with centralized search infrastructure. I think it is probably impossible to find a single information storage system that will meet everyones needs. But, it is possible to require all information systems to be discoverable if you establish the policy that any new system must be added to the company-wide search infrastructure.

# Choose your technology wisely

Unless there is a magnitude of order benefit from using a less popular technology, you should use the most popular technology. "Picking the winner" has two key benefits. First, you are more likely to find people that have experience with the more popular technology. Secondly, and more importantly, the more popular technology will likely have a richer support and development ecosystem which will likely overcome whatever initial advantage the less popular technology offered.

# Store data in standard formats

* Data should be stored in standard, lossless formats like UTF-8.
* When possible, reach to existing specifications or RFCs rather than creating your own. (These specifications were created by hardcore neckbeards of yore. You do not want to compete with them because you will almost always do a worse job.) 
* Input data should be validated when received and if valid, stored in a standard format. If invalid, the input should be rejected.

# Create an awesome, searchable corporate directory

The reason we bring people together in companies is because people are more productive when they work together. Don't squander this productivity by making it difficult to collaborate.

# Centralize authentication infrastructure

All systems must authenticate against centralized authentication infrastructure. While you don't need true SSO out of the gate, you should only need one set of credentials to access any system.
