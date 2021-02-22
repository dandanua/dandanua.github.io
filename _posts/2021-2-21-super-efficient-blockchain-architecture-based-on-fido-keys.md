---
title: Super-efficient blockchain architecture based on FIDO keys
date: 2021-02-21 17:19:00 +0200
categories: [misc]
tags: []     # TAG names should always be lowercase
--- 

## 1. Introduction

Reaching decentralized consensus is hard. Really, there must be a reason why Bitcoin network is such an energy-inefficient monster with its Proof-of-Work algorithm. Of course, many other consensus algorithms exist that are not that power-hungry, for example, Proof-of-Stake variations. But they require some sort of centralization, usually hidden under details and complexity. And they still have limits, a boundary on the total number of transactions per second that a network can process. 

I don't believe it's possible to find an efficient consensus algorithm, which would be truly decentralized. But why restrict ourselves to just software solutions? Here I want to explain that a super-efficient hardware solution exists. And it's already within the reach of our current technologies. 

## 2. The Main Problem 

What is the root cause of why it's so hard to achieve a decentralized consensus? 

Well, in the case of cryptocurrencies it's called _double spending_. If Clara has 100 coins at some point in time then she can't spend 200 coins – the network will just ignore such transaction as incorrect. But transferring 100 coins to Alice or 100 coins to Bob are both correct transactions. Yet they are conflicting. If Clara simultaneously commits both these transactions then the network has to decide what to choose as the right one and what to discard. 

Making a decision based on time is not a deterministic way, because some participants can receive one of the conflicting transactions earlier in time, while the others will see a different order. 

Somehow the whole network, i.e. every participant of it, has to decide what to pick as the correct transaction in a way, that will be in agreement with the decisions of every other participant. 

This decision can't be based on some mathematical function of a transaction (if Clara has enough freedom of composing it). For example, we can't have a rule where we pick the transaction of 100 coins to Alice as correct because _A < B_. Because if Clara commits another conflicting transaction of 100 coins to Aaron later in time, then the whole network would have to change its decisions. 

Note that the same problem exists in a broader context, not related to currencies. For example, you can have a blockchain network where users can play chess games. At any moment in time a player has a set of allowable moves. But a player is  allowed to choose only one move. If he commits two moves simultaneously, then, again, the network has to decide what to pick as the right one. 

There are many consensus algorithms invented, but they are  just ways to centralize a network, where these conflict resolution decisions are made by some selected (or elected) set of participants. In fact, Bitcoin PoW consensus algorithm is a form of election of a participant (miner), whose decision will be set as correct for the whole network. 

---

So, how can we use hardware to solve this problem?

Imagine hypothetical hardware that additionally signs Clara's transactions (via some cryptographic microchips). Moreover, this hardware keeps a counter of Clara's transactions and _attaches_ this number to a signed transaction. So, the network will see a transaction with its number that has two signatures – the one by Clara's private key and another one by hardware private key. 

Conflict resolution is trivial in this case. The network will accept a transaction that has a lower counter (it has to be +1 of the previous transaction number). 

The main assumption that we need for network security is that Clara can't tamper hardware or simulate it in some way. Of course, making tamper-proof hardware is also a hard task, but the technology is almost there. There are a lot of tamper-resistant USB keys that are already on the market.

Actually, FIDO keys are already satisfying our blockchain needs!

## 3. FIDO keys

FIDO (Fast IDentity Online) is an alliance of the world's major IT companies. They developed U2F (Universal 2nd Factor) – an open standard of two-factor authentification that uses USB or NFC keys. Luckily, this standard is indeed universal. Even though it was developed as an addition to password authentification in centralized services, we can use it in a blockchain network instead! 

A nice overview of U2F protocol you can see [here](https://developers.yubico.com/U2F/Protocol_details/Overview.html). 

In particular, look at this diagram 

![Mobile View](https://developers.yubico.com/U2F/Protocol_details/auth_flow4.svg)_https://developers.yubico.com/U2F/Protocol_details/Overview.html_

It is supposed that a Relying Party (server) starts second-factor authentification by sending a _challenge_ to a client. But blockchain network has no servers. Instead of a challenge, Clara uses the hash of her transaction, where _app_id_ is just a name of the blockchain network and _handle_ is Clara's personal public key. Then the U2F device will update the counter and return the signed transaction with the attached counter. This is exactly what we need! Clara then sends this information along with the actual transaction and her own signature to the network. 

To ensure that Clara can't simulate the device, U2F protocol additionally includes the attestation certificate of the device manufacturer, as explained [here](https://fidoalliance.org/fido-technotes-the-truth-about-attestation/). 

## 4. Typical usage scenario

First of all, a user has to register in the network. To do this, he needs to have FIDO hardware key and to generate his personal private/public keys. The registration is essentially linking personal keys with device keys. For this, an empty challenge can be used in the device signing flow. 

Making transactions was described in the previous section.

If the device is lost/broken/stolen then the user can register another device with its personal key. Though, in this case, the network should wait for some time, for example, 24 hours, to be sure that the previous device (in a couple with the personal key) is not used anymore.

## 5. Threats 

Since every transaction must have two signatures – by device and user, the device alone can't be used to harm its user. 

But an incorrect or tampered device can be used to submit conflicting transactions to the network (for example, with the same counter). This will split the network if no other consensus algorithm is involved.  

But we can assume that manufacturer has no incentives to make incorrect devices to harm the network. 

So, the only threat is device tampering. Even though modern FIDO keys are tamper-resistant, their security is probably not enough to be used in blockchain networks, because there were no reasons to make them fully tamper-proof in usage with centralized servers. But nearly tamper-proof technology is certainly possible. 


## 6. (Super) Efficiency

As for this blockchain efficiency, it's certainly unreachable for ordinary blockchains. Because in this architecture, no additional communication to resolve possible conflicts is needed ___AT ALL___. Every possible conflict is resolved immediately in an independent and coherent fashion. 

## 7. Final Words

This is, of course, only a high-level overview of blockchain architecture. A lot of details are skipped. But I hope it can be seen that this architecture is a real solution to a lot of blockchain problems. 

---

_[Discussion](https://news.ycombinator.com/item?id=26214175) on HackerNews._