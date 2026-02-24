---
layout: post
title: 18631 Information Security ~ Notes
subtitle: CMU-18631 信息安全课程笔记
categories: notes
banner:
    image: assets/images/banners/article-crypt-notes.jpg
excerpt: "CMU-18631 信息安全课程笔记"
image_source: "https://www.pixiv.net/artworks/134859471"
toc: false
published: false
---

Notes for CMU-18631 Introduction to Information Security.

## Lecture 1: Intro

Grading:

+ Participation (quizzes) 10%
+ Homework assignments 30%
+ Tests (Test 1, 2, 3) 45%
+ Case study 15% (10+5)

## Lecture 2-3: Threat Model

Goal:
+ Model the system
+ Model the threats

How to threat model? Think about
+ What are you building?
+ What can go wrong?
+ What should you do about things than can go wrong?
+ Did you do a good job of analysis?

How to model the system? Think about
+ Diagrams of the system
    + What are the components?
    + How data flow through the system?
+ Trust boundaries
    + Who control/have access to which components?
+ Attack surface
    + The set of APIs that an attacker can launch an attack against.

Mind set in threat modeling:
+ Focusing on asset
    + How an attacker could threaten each asset?
+ Focusing on attackers
    + What attacker would do?
+ Focusing on software and system
    + Build models that focus on software being built, or system being deployed
    + Detail assumptions made

Boolean attack tree/Parameterized attack tree:
+ Nodes: attack steps
+ Parameters: can be costs, probabilities, etc.

Threat model by Microsoft, STRIDE:
+ Spoofing of user identity
+ Tampering
+ Repudiation
+ Information disclosure (privacy breach or data leak)
+ Denial of service (D.o.S)
+ Elevation of privilege

Security requirements (properties):
+ Secrecy, privacy, and confidentiality
    + Keeping information secret from all but those who are authorized to see it
    + Privacy = preserving own personal information secret
        + Alice protects her privacy by not sharing her address to strangers
    + Confidentiality = obligation to preserve someone else's information secret
        + Trent ensures confidentiality of Alice's credit card numbers
    + Secrecy = effect of mechanisms used to limit the number of principals who can access information
+ Integrity
    + Ensuring that information has not been altered by unauthorized or unknown means
+ Authentication/Identification
    + Corroboration of the identity of an entity
    + Note that identification can be pseudonymous
+ Message Authentication
    + Corroborating the source of information
    + Also known as _data origin authentication_
+ Anonymity
    + Concealing identity of a protocol participant
+ Non-repudiation (Accountability)
    + Assurance that someone cannot deny something
+ Freshness
    + Proof that an event occurred after a given point in time
+ Age
    + Proof that an event occurred before a given point in time
+ Availability
    + Services/resources are available to rightful entities
+ Others
    + Authorization
        + Conveyance to another entity of official sanction to **do** or **be** something (someone)
    + Certification
        + Endorsement of information by a trusted entity
    + Access control
        + Restricting access to resources to privileged entities
    + Witnessing
        + Verifying the creation or existence of information by an entity other than the creator
    + Revocation
        + Retraction of certification or authorization

## Lecture 4: Symmetric Key Cryptography

+ **Cryptology** is the study of Cryptography and Cryptanalysis
+ **Cryptography** is the study of mathematical techniques to enforce security properties
    + Only (one of many) means to an end
+ **Cryptanalysis** is the study of how to break cryptographic systems

Caesar cipher:
+ Shift each letter by a fixed amount (e.g., 3)
+ E.g., "HELLO" → "KHOOR"
+ Fragile to frequency analysis attack
+ How to destroy the structure?
    + Encrypt more than one letter at a time (e.g. Playfair cipher, digraph cipher)
    + Polyalphabetic substitution (e.g. Vigenère cipher):
        + Use different monoalphabetic substitutions as one progresses through the message
        + A set of related monoalphabetic substitution rules is used
        + A key determines which particular rule is chosen for a given transformation
    + Autotext (i.e. Autokey cipher):
        + Use the plaintext itself as part of the key
        + However, it is still vulnerable to frequency analysis attack
+ Idea: Use a key as long as the text and statistically independent

One-time pad:
+ A stream cipher based on XOR
+ Problem:
    + True randomness is hard
    + Impractical for large messages

Rotor machines:
+ Use multi-stage encryption
+ Each stage consists of a rotor, that performs a monoalphabetic substitution
+ Once a key is pressed the rotor shifts by one position
+ So, for one rotor, polyalphabetic substitution of period 26
+ Power is in using multiple rotors
+ Once the first rotor has completed a full revolution, the second rotor advances by one pin, and so forth

Symmetric Key Cryptography:

$$
P=D_k(E_k(P)), K_1=K_2=K, D_k=E_k^{-1}.
$$

Both parties share the same key $K$.

Stream ciphers:
+ Attempt to approximate a one-time pad
+ Advantage: easy to implement in hardware
+ Disadvantage:
    + Key must never be used twice
    + Bit flipping attack: if the attacker flips a bit in the ciphertext, the corresponding bit in the plaintext will also be flipped, even if the attacker does not know the key (breaks integrity).

Block ciphers:
+ Operate on blocks of n bits of plaintext and ciphertext
+ The same plaintext block is encrypted using the same key, and always encrypts to the same ciphertext block
    + Examples: DES, AES, IDEA, ... Most "popular" ciphers

Feistel cipher:
+ A method of constructing a block cipher from a function $f$ and a key $K$.
+ The plaintext is split into two halves, $L_0$ and $R_0$.
+ For each round $i$, the following operations are performed:
    + $L_i = R_{i-1}$
    + $R_i = L_{i-1} \oplus f(R_{i-1}, K_i)$
+ After $n$ rounds, the ciphertext is formed by concatenating $R_n$ and $L_n$.
+ Advantage: the function $f$ does not need to be invertible, which allows for more flexibility in the design of the cipher.

Data Encryption Standard (DES):
+ Block size = 64 bits
+ Key size = 64 bits, but only 56 bits effective (the remaining 8 bits are used for parity checking)

Advanced Encryption Standard (AES):
+ Support block sizes of 128, 192, or 256 bits and key sizes of 128

Electronic Codebook (ECB) mode, UNSAFE:
+ Each block of plaintext is encrypted independently using the same key.
+ Identical plaintext blocks will produce identical ciphertext blocks, which can reveal patterns in the plaintext.

Triple DES:
+ Why not Double DES?
    + Meet in the middle attack only takes $O(2^{56})$ time to break it.

## Lecture 5: Hash-based Algorithms

One-way Hash Functions:
+ One-way-ness: Given $y=H(x)$, can not easily find $x$.
+ Weak collision resistance: Given $x$, can not easily find $x' \neq x$ such that $H(x) = H(x')$.
+ Strong collision resistance: Can not easily find any two distinct inputs $x$ and $x'$ such that $H(x) = H(x')$.
+ Arbitrary input size, fixed output size

Can we do...
+ Data Integrity by sending $(m,H(m))$?
    + No. An attacker can modify $m$ to $m'$, and compute $H(m')$ to replace $H(m)$.
+ Message Authentication by sending $(m,H(m))$?
    + No. An attacker can modify $m$ to $m'$, and compute $H(m')$ to replace $H(m)$.
+ Secrecy by sending $H(m)$?
    + It is secret, but it is not useful for the receiver to recover $m$.

Birthday attack:
+ To find a collision of a hash function with an output size of $n$ bits, an attacker only needs to compute $O(2^{n/2})$ hashes, which is much less than the $O(2^n)$ hashes needed for a brute-force attack.

One-way Hash Chain:
+ One-way function $F$
+ Random value $r_n$ (keep secret)
+ Construct from $r_n$ to $r_0$ by $r_i = F(r_{i+1})$ for $i=n-1,...,0$.
+ Efficient one-way verification: 
    + Given $r_i$ and $r_j$ with $i<j$, we can verify that $r_i$ is derived from $r_j$ by applying $F$ for $j-i$ times.
+ Usage: one-time password
    + User generate $r_n$ and compute $r_0$ by applying $F$ for $n$ times, and register $r_0$ to the server.
    + Each time the user wants to login, it sends $r_i$ to the server, and the server verifies that $r_i$ is derived from $r_0$ by applying $F$ for $i$ times. After successful verification, the server updates the stored value to $r_i$ for the next login.

MD5:
pending...

MAC:
pending...

## Lecture 6: Asymmetric Key Cryptography & PKI

Key distribution problem:
+ For $n$ participant in the system, $O(n^2)$ keys are needed for pairwise symmetric key encryption.

Diffie-Hellman-Merkle key exchange protocol:
+ Public: a large prime $p$ and a generator $g$
+ Alice chooses a random secret $a$, computes $A=g^a \mod p$, and sends $A$ to Bob.
+ Bob chooses a random secret $b$, computes $B=g^b \mod p$, and sends $B$ to Alice.
+ Alice computes the shared secret key as $K = B^a \mod p$.
+ Bob computes the shared secret key as $K = A^b \mod p$.
+ Both Alice and Bob arrive at the same shared secret key $K = g^{ab} \mod p$.
+ Eve cannot compute $K$ without solving the discrete logarithm problem.
+ But an eve can perform a Man-in-the-Middle attack
    + Eve intercepts $A$ and $B$, and sends her own values $E_A$ and $E_B$ to Alice and Bob, respectively.
    + Alice computes $K_{AE} = E_B^a \mod p$, and Bob computes $K_{BE} = E_A^b \mod p$.
    + Eve can compute both $K_{AE}$ and $K_{BE}$, and can decrypt messages from Alice and Bob, and re-encrypt them to forward to the other party.

Public Key Cryptography (Asymmetric Key Cryptography):
+ Each participant has a pair of keys: a public key and a private key.
+ The private key is kept secret, while the public key is shared with everyone.
+ The public key is used for encryption, and the private key is used for decryption.
+ Allows encryption and authentication.
+ Requirement:
    + Extremely hard to guess the private key from the public key/ciphertext.
    + Easy to compute the ciphertext from the plaintext and the public key, and to compute the plaintext from the ciphertext and the private key.

RSA:
+ Choose two large prime numbers $p$ and $q$
+ Compute $n = pq$ and $\phi(n) = (p-1)(q-1)$
+ Choose an integer $e$ such that $1 < e < \phi(n)$ and $\gcd(e, \phi(n)) = 1$
+ Compute $d$ such that $de \equiv 1 \mod \phi(n)$
+ Public key: $(n, e)$
+ Private key: $(n, d)$
+ Encryption: $C = M^e \mod n$
+ Decryption: $M = C^d \mod n$
+ Security relies on the difficulty of factoring large integers.

Digital Signature:
+ Sign a message with private key, and anyone can verify the signature with the public key.

Secrecy + Integrity + Authentication:
+ $K_A,K_B$ are private keys, and $K_{A'},K_{B'}$ are public keys.
+ Sign and encrypt: $C = E_{K_{B'}}(M || S_{K_{A}}(M))$
+ When Bob receives $C$, he decrypts it with his private key $K_B$ to get $M$ and the signature, and then verifies the signature with Alice's public key $K_{A'}$.

Public Key Infrastructure (PKI):
+ Alice wants to verify that a public key belongs to Bob
    + Otherwise, Eve can impersonate Bob by generating her own key pair and claiming that the public key belongs to Bob, then Eve can decrypt messages intended for Bob and re-encrypt them to forward to Bob, effectively performing a Man-in-the-Middle attack.
+ Certificate Authority (CA):
    + Similar to a trusted third party (TTP)
    + Vouch for the authenticity of public keys
    + Issuers of public-key certificates
+ PKI Trust Model:
    + Delegate CA: CA can delegate its authority to other CAs, which can further delegate to more CAs, forming a hierarchical trust model.
    + Monopoly CA: A single CA is responsible for issuing certificates for all entities in the system.
    + Monopoly + trusted Registration Authority (RA): RA can check and vouch, but only CA can sign.
    + Oligarchy: Multiple trusted anchors, configurable.
    + (Possible...) Consensus-based protocol: notaries vote on the validity of a certificate, and a certificate is considered valid if it receives enough votes.
+ Problem:
    + Too many "trusted" CAs. If any one of them is compromised, the security of the entire system is compromised.
        + Practical solution: pinning
            + Hard Certificate Pinning: Hardcode the exact server certificate into apps
            + CA Pinning: Limited set of authorities, or a particular public key of server

Certificate Revocation:
+ CRL (Certificate Revocation List): A list of revoked certificates published by the CA.
    + Problem: High cost, not real-time...
+ OCSP (Online Certificate Status Protocol): A protocol for checking the revocation status of a certificate in real-time.
    + Problem: Privacy concern, as the OCSP responder can track which websites a user is visiting.
+ General problem:
    + We assume that a certificate is valid until it expires, but in reality, a certificate can be compromised before its expiration date, and there is no efficient way to revoke it.

## Lecture 7: Access Control in Operating Systems

Model:
+ Source-Request-GUARD-Resource
+ Authorization: Determining who is trusted to access the resource

Policy vs Mechanism:
+ Policy: What is allowed and what is not allowed?
+ Mechanism: How to enforce the policy?

Trusted Computing Base (TCB):
+ The set of components that must function properly for the system to be secure
+ As small as possible

Principles for Access Control:
+ Economy of mechanism: Keep the design as simple and small as possible.
+ Fail-safe defaults: Default to no access, and require explicit permission to grant access.
+ Complete mediation: Every access to every resource must be checked for authorization.
+ Open design: The security of the system should not depend on the secrecy of the design or implementation.
+ Separation of privilege: Require multiple conditions to be satisfied for access to be granted.
+ Least privilege: Give each user and process the minimum privileges necessary to perform their tasks.
+ Least common mechanism: Minimize the amount of sharing. Every shared mechanism (e.g., shared variables) represents a potential (untrusted) information path.
+ Psychological acceptability: The access control mechanism should not make the resource more difficult to access than if the security mechanism were not present.

Simple Access Control Policies:
+ Unprotected: e.g. DOS
+ Complete isolation: users are in sandboxes. e.g. virtualization
+ Controlled sharing: control explicitly which users can access which resources. e.g. file permissions in Unix

Access Matrices:
+ A triplet (User, Resource, Access), e.g. (Alice, file, write)
+ Access Control List (ACL): For each resource, list the users and their access rights.
    + E.g. file1: (Alice, read), (Bob, write)
+ Capability List: For each user, list the resources and their access rights.
    + E.g. Alice: (file1, read), (file2, write)

| |ACL|Capability List|
|-|-|-|
|Revocation of access of one object|Easy (one list)|Hard (track down all caps)|
|Revocation of one subject|Not easy (all lists)|Not easy (all caps for that subject)|
|Eventual revocation|Easy (create no more caps)|Hard (track down all caps)|
|Giving access to new subjects|Update relevant objects|Issue new caps (easy)|
|Delegate|Difficult|Easy|
|Enumerating legitimate users|Easy|Hard|
|Enumerating accessible objects|Hard|Easy|

+ Discretionary Access Control
    + The user owning an object decides how other users can access the object
    + Example: UNIX Access Control
+ Mandatory Access Control
    + Each object has a sensitivity level associated with it, and users have clearances for different sensitivity levels
    + Example: Military security clearances
+ Role-based Access Control
    + Access rights are assigned to roles, and users are assigned to roles

Access Control in UNIX:
+ Optimization: group by accessor types
    | Accessor | Access to File object X |
    |----------|-------------------------|
    | owner    | read, write, execute    |
    | group    | read, execute           |
    | others   | read                    |
+ ACL
    + store rights with the target object to be accessed
    + ACL representation is compressed to 9 bits (r,w,x * 3 accessor types)
    + Primitive version; has been extended...

## Lecture 8: Password and MFA

## Lecture 9: Multilevel and Information Flow Security

## Lecture 10: Software/Hardware Security

