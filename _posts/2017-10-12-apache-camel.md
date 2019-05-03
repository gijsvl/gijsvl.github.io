---
layout: post
title: Apache Camel's Not-So-Secure Crypto
date:   2017-10-12 12:00:00
description: One of our developers struggled trying to use Apache Camel’s crypto library. As expected of a good developer he was worried about the security of the software he was writing. He figured out that some things are wrong with the way the library is doing encryption. Therefore, I took a look at the library myself, and figured they are making quite a few cryptographic mistakes that diminishes the security of the encrypted text.
---

One of our developers struggled trying to use Apache Camel’s crypto library. As expected of a good developer he was worried about the security of the software he was writing. He figured out that some things are wrong with the way the library is doing encryption. Therefore, I took a look at the library myself, and figured they are making quite a few cryptographic mistakes that diminishes the security of the encrypted text.

## TL;DR
- Insecure defaults: without any parameters the encryption will be DES, using CBC mode and PKCS5 padding.
- MAC-then-Encrypt: one should use Encrypt-then-MAC instead.
- Same key for encryption and MAC

## INSECURE DEFAULTS
The problem with insecure defaults is that I want to tell developers to trust the crypto, not to build it themselves. But use the built-in cryptographic functions in common libraries we generally trust, and extend the trust to the security of the library. With a bare minimum knowledge of cryptography they should be able to secure data both in transit as well as at rest. Unfortunately, the libraries expect the developer to define the proper algorithms, if the developer doesn’t, then the library will default to insecure ciphers. Therefore, all hope is lost.

While the Data Encryption Standard (DES) was once an accepted cipher, by now, we long know it can be broken in less than a day. While the standard in itself can maximally ensure security of 56 bits, since 2008 the best known attack has only 43 bits complexity.

When a developer doesn’t define an algorithm while using Apache camel’s crypto library, the library will default to DES. Leading to the above insecurity, in which case a developer needs more thorough cryptographic knowledge to actually use the crypto library.

## MAC-THEN-ENCRYPT
A second problem is the order that they use for their encryption and signing processes. There are basically three ways of doing this, but only one guarantees security:

1. Encrypt-and-MAC: One will encrypt the plaintext and concatenate this with a MAC of the plaintext. Clearly, the MAC will reveal some information about the plaintext. Very often MACs are even deterministic, therefore, an adversary would be able to recognise when the same message was encrypted twice, this breaks IND-CPA, which is a theoretical definition of a secure encryption scheme.
2. Mac-then-Encrypt: This is the method Apache Camel is using. In this method, one computes a MAC of the plaintext and then encrypts a concatenation of the plaintext and the MAC. At first, this might seem like a good approach, as it was also used in older versions of SSL. Unfortunately, this lead to attacks such as Lucky13 and BEAST.
3. Encrypt-then-MAC: This is the only secure method. One does encrypt the plaintext and concatenates this with a MAC of the cipher text.
A nice but theoretical overview of this problem can be found in this paper by Bellare.

## SAME KEY FOR ENCRYPTION AND MAC
Finally, Apache Camel requires one key as an input, this key is used for both the encryption algorithm as well as the MAC algorithm. Although, this is not necessarily a security issue, for some choices of encryption algorithms in combination with a certain MAC algorithm this could lead to vulnerabilities. Therefore, this is generally considered bad practice. Moreover, because the security proofs don’t account for key reuse in different algorithms, the algorithms cannot guarantee security.

## CONCLUSION
Apache Camel’s crypto library should not be used. If you would like to add encryption to your workflow, use a more vetted standard library such as BouncyCastle.
