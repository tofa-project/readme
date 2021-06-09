## Intro
Tofa is an open source two factor authenticator (2FA) built on top of Tor, thus inheriting 2 core properties: it's anonymous and decentralized. Tofa does not require third party involvment between cloud server and client. The communication is done End-To-End (E2E) between service and client ensuring the privacy of both parties. It can be integrated easily with onion and clearnet services. 

## How it works
I will not dive deep into technical aspects. The following scheme should offer an overall image:

![alt text](https://github.com/tofa-project/readme/blob/main/img/scheme.png?raw=true)

Tofa Client is an application installed on a machine belonging to human, who is a client of Service 1, 2, X. It hosts onion services dedicated for each of Service 1, 2, X

Whenever one of Service 1, 2, X requires a 2FA related confirmation from user's side (eg: password reset, funds withdrawal, identity confirmation) it contacts the onion services hosted by Tofa Client directly. Tofa Client will show a pop-up which alerts the human asking for confirmation. 

The communication is done entirely via Tor.

## Authentication flow
1. User creates an app (onion service) in its Tofa Client
2. Cloud Service registers with the app from Tofa Client. Registration occurs with human confirmation on Tofa Client. If registration is allowed, Cloud Service receives an authentication token which must be provided anytime it contacts Tofa Client. Registration can occur only once per app!
3. Cloud Service can contact the app from Tofa Client using the authentication token provided from step 2.

## Security concerns

List compiles security flaws which I have thought about. There may be more but time will tell it.

#### A) MITM attacks
Tofa relies on the TLS nature of Tor circuits. Since communication is done onion service <-> cloud service, MITM attacks are not a high concern due to how Tor protocol works.

#### B) Impersonation
Impersonation can occur on two sides: Client impersonation and Service impersonation

Client impersonation can happen if an attacker steals the private key of the onion service. 

Service impersonation can happen if an attacker steals the connection URI and the authentication token (which is app's ID on Tofa Client database).

Due to how Tor works MITM attacks are improbable, thus impersonation can occur if attacker compromises Client's or Service's machine. Tofa Client takes an extra precaution measure by encrypting its local database with AES.

#### C) Denial of Service (DoS)
DoS can occur on two sides: Client DoS and Service DoS

Client DoS can happer if attacker floods the onion service with requests. Fortunately Tofa Client closes the onion service automatically once certain threshold of bytes/requests is hit.

Service DoS can occur if a malicious Tofa Client replies malicious response to Service when the latter one requests confirmation from it. Adaptors do not have an anti-DoS feature yet!

