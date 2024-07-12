---
layout: post
published: true
date: 2024-07-15 14:00:00
title: Swift Homomorphic Encryption for Live Caller ID Lookup
author: [fabian-boemer, karl-tarbe, rehan-rishi]
---

We're excited to announce a new open source Swift package: [Swift Homomorphic Encryption](https://github.com/apple/swift-homomorphic-encryption).

Swift Homomorphic Encryption is an implementation of the cryptographic protocols homomorphic encryption (HE) and private information retrieval (PIR), which enables privacy-preserving solutions for server side lookups.
Below is a description of how it works in the context of [Live Caller ID Lookup](https://developer.apple.com/documentation/sms_and_call_reporting/getting_up-to-date_calling_and_blocking_information_for_your_app), a new feature of iOS 18.

In the Live Caller ID Lookup feature, the phone number of an incoming call is used to obtain information from a server about the caller without revealing the incoming phone number to the server. A second swift package, [Live Caller ID Lookup Example](https://github.com/apple/live-caller-id-lookup-example), shows how to implement the server part of the Live Caller ID feature using the PIR implementation available in Swift Homomorphic Encryption.

Thanks to the Swift ecosystem, Swift Homomorphic Encryption:
* provides cross-platform support for Linux and macOS
* benchmarks performance using the [benchmarking](https://github.com/ordo-one/package-benchmark) library
* uses the performant low-level cryptography primitives in [swift-crypto](https://github.com/apple/swift-crypto).

## Homomorphic Encryption (HE)

Swift Homomorphic Encryption implements a special form of cryptography called *homomorphic encryption (HE)*.
HE is a cryptosystem which enables computation on encrypted data.
The computation is performed directly on the encrypted data, *without revealing the plaintext of that data to the operating process*.
HE computations therefore happen without decryption or access to the decryption key.

HE thereby allows a client to enable a server to perform operations on encrypted data, and therefore without revealing the data to server. A typical HE workflow might be:

* The client encrypts its sensitive data and sends the resulting ciphertext to the server.
* The server performs HE computation on the ciphertext (and perhaps its own plaintext inputs), without learning what any ciphertext decrypts to.
* The server sends the resulting ciphertext response to the client.
* The client decrypts to learn the response.

Swift Homomorphic Encryption implements the Brakerski-Fan-Vercauteren (BFV) HE scheme.
BFV is a *leveled HE scheme*, meaning it supports encrypted additions and multiplications, whereas *partial HE schemes* support only a limited number of additions or multiplications.
This makes leveled HE a better fit for more complex applications.
After its invention in 2012, BFV has gained adoption in academia and industry, including several optimizations and implementations.
BFV is based on the ring learning with errors (RLWE) hardness problem. RLWE, and therefore BFV can be configured to provide post-quantum 128-bit security, making it future-proof.
The Live Caller ID feature requires BFV parameters with post-quantum 128-bit security.

HE enables a new paradigm of privacy-preserving applications, and we’re excited to see what the community will build.

## Private Information Retrieval (PIR)
One application of HE is private information retrieval, (PIR), a form of private keyword-value database lookup.
In the PIR setting, a client has a private keyword, and wants to retrieve the associated value from a server hosting a keyword-value database.
Because the keyword is private, the client wants to perform this lookup, *without the server learning the keyword*.

A trivial implementation of PIR is to have the client issue a generic "fetch database" request, independent of its private keyword.
Then the server server sends the entire database to the client.
While this *trivial PIR* protocol satisfies the privacy and correctness requirements of PIR, it is only feasible for small databases with infrequent updates.

The PIR implementation in Swift Homomorphic Encryption uses HE to improve upon the trivial PIR protocol.
The HE-based PIR implementation supports larger databases by requiring only a small client download each time the database is updated.
This allows PIR to scale to larger databases with more frequent updates.

## Live Caller ID Lookup

[Live Caller ID Lookup](https://developer.apple.com/documentation/sms_and_call_reporting/getting_up-to-date_calling_and_blocking_information_for_your_app) is an app extension coming in iOS 18.
This extension allows app developers to provide caller ID and call-blocking services from their own hosted servers.
When a user’s iPhone receives a call, the system will communicate with the app server to retrieve call-blocking information and caller ID, which are then displayed on the device’s incoming call screen.
Live Caller ID Lookup performs the retrieval using PIR, to ensure the incoming phone number is not leaked to the app developers’ servers.

## Get Involved

Follow the open-source repositories [swift-homomorphic-encryption](https://github.com/apple/swift-homomorphic-encryption), [live-caller-id-lookup-example](https://github.com/apple/live-caller-id-lookup-example) for latest developments.
We encourage you to file an issue via [https://github.com/apple/swift-homomorphic-encryption/issues](https://github.com/apple/swift-homomorphic-encryption/issues), [https://github.com/apple/live-caller-id-lookup-example/issues](https://github.com/apple/live-caller-id-lookup-example/issues) if you encounter any problems or have suggestions for improvements.
Please try out the code, and open issues or report any feedback.
We’re excited to see how homomorphic encryption can empower the Swift community to enable new use-cases.
