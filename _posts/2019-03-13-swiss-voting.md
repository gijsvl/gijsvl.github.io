---
layout: post
title: My Take on the Swiss E-voting System
date:   2019-03-13 12:00:00
image: swiss-evoting.png
description: 
---

While wild discussions about the cryptography that has to protect the Swiss elections in the future broke loose on [Twitter](https://twitter.com/SarahJamieLewis/status/1101253491299766272), I was looking at the code. I don't know where to start, I don't know how to review it. Nevertheless, I have been here before, it's that first time you get into a large enterprise Java project, it is haunting, it is scary. But step by step I surf through the code, try to find beginning and end (after IntelliJ has indexed the whole project of course). The code is not easy to read and during previous projects I had someone that was familiar with the code that I could ask questions, but this time I was all alone in this code. You quickly lose yourself in time. 

I'm talking about [Scytl's e-voting system that the Swiss Post is actively testing](https://www.onlinevote-pit.ch/) to use in a real election in the near future. By best practice they started a bug bounty program such that anyone who wanted to have a look at a live running system or at the code could do so. My advisor's interest was immediately triggered, leading to the Twitter storm I was talking about earlier. Nevertheless, I wanted to have a look at it as well. Although some of the researchers found a huge cryptographic flaw in the system, I didn't stumble upon any interesting flaw. But I was astonned about the whole project as much as the other researchers. And I have to agree with them, if you wanna run elections on a software system, someone needs to able to read and audit the code (other than the creators).

## Try to compile

First job seemed easy enough, try to get the whole project to compile. Not too much to say about this, I succeeded, but it took me way too much time. Moreover, compiling is not running, far from it. A few remarks though:
- Remove all links to the private maven repositories of the developers which I don't have access to.
- JNI needs the underlying C library installed, GMP in this case, a lot of code to package this native code within the project. Either get rid of it or try to compile.
- Some dependencies are not public, e.g. Oracle ojdbc8.jar can be downloaded from the Oracle website, but requires an account. The not-so-open-source projects...

## Auditability of Code

This is probably the most important part of my post. When we want a system that is gonna protect our democracy during the coming years, or any other security sensitive system for that matter, we need great auditability. This is of course a matter of trust, do you trust the company providing the code enough to let them run the elections. And the answer to this question is easy, there shouldn't be any company that you should trust with this type of applications. The solution to this is auditable code.

I do not necessarily trust the company, but I trust someone who trusts someone that can really read the code and audit the system. In this kind of web of trust, we don't all have to be computer scientists, we just have to know some in a close trust relationship to extend the trust to the system at hand. I used to be a sceptic about this point, assuming we all had to be able to verify a system. However, I believe trust in a system can be inferred from this type of more personal trust relationships.

Which brings me to putting trust in a system such as the one developed by Scytl. I was not able to audit the code properly, my advisor was not able to audit it properly, other very good researchers in my field didn't succeed in auditing the code properly, [yet they found a huge flaw anyway](https://motherboard.vice.com/en_us/article/zmakk3/researchers-find-critical-backdoor-in-swiss-online-voting-system). And exactly here is were this web of trust idea collapses. All people that I would have trusted with reviewing the code, maybe even including myself, didn't succeed in given the assurances needed for complete trust in this system. 

I don't want to make high statements here, I know how difficult such huge projects can be, but we really need to come up with better ways of building readable and maintainable code. Trust is a very fragile notion, and as computer scientists we are way too bad in explaining things in such a way that trust can follow automatically. I appreciate what both the Swiss Post and Scytl are trying to do here, being transparent, but at this point it isn't enough yet. 
