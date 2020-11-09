# Cryptography

## Overview

### What You'll Learn
* What cryptography's all about
* A variety of different cryptographical techniques
  * Symmetric Cryptography:
    * Stream Ciphers
    * Block Ciphers
  * Authenticated Encryption 
  * Asymmetric (Public-Key) Cryptography:
    * Trapdoor Permutation
    * Diffie-Hellman 
* How to apply them

### Prerequisites

Before starting this section, you should have an understanding of

1. [Basic Python](https://github.com/HackBinghamton/PythonWorkshop) (for interactive sections only)

## Introduction
### What is cryptography?
Cryptography is a means of securing information to prevent **eavesdropping** and **tampering**. If you're only slightly familar with cryptography, you probably know it is a way of passing secret messages, but we also use cryptography to protect network traffic on the web, over wifi, and through bluetooth; to encrypt files on your disk drive; to access content locked into a specific platform (ie. songs you own on iTunes); and to verify your identity online.

As an example, encrypting communication over a network has two main steps: establishing a shared key using public key cryptography and then transmitting data using that shared secret key. These two steps are the basis of most traditional encryption. Non-traditionally, encryption can also be used to establish a digital signature or to communicate anonymously.

*Source:* This workshop is based off material from Dan Boneh's free online cryptography course.

---
### Simple Example Ciphers
To start, we'll begin with some ciphers that were used seriously in the past but nowadays with advances in computing are easily broken. You should only use these ciphers recreationally. 

First let's establish some notation. A *symmetric cipher* refers to an crypto system where both the encrypting party and the decrypting party use the same secret key. Our encryption and decryption algprithms are E(k,m) and D(k,c), where k is our key, m is our plaintext message, and c is our ciphertext. E(k,m)=c and D(k,c)=m. To formally define this we say a symmetric cipher is a cipher defined over (K, M, C) is a pair of efficient algorithms (E,D) where E: K x M -> C, D: K x C -> M, such that for every m in M and k in K, D(k, E(k,m)) = m. E is often a randomized function where D is always deterministic. 

So, if Professor Les Lander wants to send a secret message to Professor Eileen Head to tell her the name of his favorite student in CS 140, m = "Bill Gates", he feeds m and the shared secret key into his encryption algorithm E(). This gives him the secret message "dfhyugilg6f", also known as a ciphertext. He then sends his least favorite student to Professor Head with this note, but they can't read it! Only Professor Head can, because she already has the secret key, k, and the decryption algorithm D(). Plugging the key and the ciphertext into D(), she reads the output and knows to watch out for Bill Gates. 

#### Substitution Cipher
A typical substitution cipher has no key. E is a bijective function from the alphabet to the alphabet and D is its inverse. Consider an example where in E: H maps to C, B maps to A, and U maps to T, while in D: C maps to H, A maps to B, and T maps to U. With these functions if we want to send a friend the message "HBU" to remind them to come to hackBU, we'll hand them the ciphertext "CAT".


#### Vigener Cipher
The Vigener Cipher is slightly more complicated than a substitution cipher. For it, we need to establish a bijection between the alphabet and [0,25]. Then we take our key and repeat it until it is the length of our message. For example, with the message COMETOHACKBU and a key, KEY, we would expand our key to KEYKEYKEYKEY. Then we use our bijection to map our expanded key and our message into integers. Adding each digit of these integers mod26 and then putting them through the inverse of the bijection produces your ciphertext. To continue our example, we have 2_14_12_4_19_14_7_0_2_10_1_20 and 10_4_24_10_4_24_10_4_24_10_4_24. Added mod26 (taking the remainder after dividing by 26) these digits are 12_18_11_14_23_13_17_4_1_20_5_19, which leads us to the ciphertext MSLOXNHEBUFT. To decrypt that we take it back to numbers, subtract our expanded key digitwise mod26, and then map back to letters.r

#### How to Break Any of These Ciphers
We can break these ciphers, along with more complicated outdated ciphers like rotor machines (heard of Enigma?) and DES with a brute force method due to the spread of modern computers. To break them even faster we can consider clues from the ciphertext. As the substitution cipher is a direct bijection, we can use what we know about letter frequency in standard sentences to speed up our search. For example, E is the most common letter in English sentences and Z is not -- so the most common letter in our cipher is most likely the image of E and unlikely to be the image of Z. We can also do this looking for common pairs of letters such as TH. We can use this idea with the Vigener cipher to try to identify our key. The most common letter - 4 [E] is most likely the first letter of the key.

---

### Discrete Probability: Reminders
Let U be a finite set. Then a *probability distribution P over U* is a function P, where U maps to [0,1], such that the summation of P(x) over all x in U is 1. A subset A of U is called an *event* and the probability of A is the summation of P(x) over all x in A. This summation will be an element of [0,1]. A *random variable* is a function X, that maps U to V. X takes values on V and produces a distribution on V.
Events A and B are *independent* if the probability of A and B (and meaning intersection) is the same as the probability of A times the probability of B. Random variables X,Y are independent if for all a,b in V the probability that X=a and Y=b is the same as the probability X=a times the probability that Y=b.

## Stream Ciphers

Stream ciphers are ciphers that return a new encrypted piece of data for every piece passed into it -- that means that it generates a piece of key material for every piece of plaintext it receives. This gives certain advantages and disadvantages.

### One Time Pad

The One Time Pad is the first secure cipher. It can encrypt messages that consist of 0s and 1s into ciphertext that consists of 0s and 1s using a randomized key of 0s and 1s the same length as the message. To calculate the ciphertext we XOR each bit of the message with the corrosponding bit of the key. To decrypt we do the same between the key and the ciphertext. This cipher encodes and decodes very fast, but because it has keys as long as the messages and each key can only be used once, the process of exchanging the keys securely is exactly as hard as exchanging the messages was in the first place. Ignoring that, is the One Time Pad secure?

### Theory: Perfect Secrecy
Let's define security under the assumption an attacker only has access to the ciphertext. A basic definition would focus on our main concerns: that the attacker cannot access our secret key or our plaintext message. The formal definition accepted by cryptographers is that of *perfect secrecy*. We consider a cipher as having perfect sercery if for every m<sub>0</sub>, m<sub>1</sub> in M where m<sub>0</sub> is the same size as m<sub>1</sub> and every c in C, the probability E(k, m<sub>0</sub>) = c equals the probability E(k, m<sub>1</sub>) = c where k is randomly chosen from K. This means that given two messages and a ciphertext our attacker could not tell which of the messages produced that ciphertext.

By this definition, we can prove the One Time Pad is secure. We won't--but we **could**. We will however note in closing, that to acheive this definition of perfect secrecy, a cipher must use a key equal in length or longer than our message. 

### Pseudorandom Generators
As we noted initially, the One Time Pad is not a particularly useful cipher because its key is as long as its messages. An idea to reduce the length of key while minimising the loss of security is to utilize pseudorandom key generators instead of random key generation. The pseudorandom generator is a function, G, that maps from a seed space to the larger key space. For an example of how they would work, consider the One Time Pad. If we use a key, k, smaller than the length of the message, and our pseudorandom generator, G, our new encryption function is E(k,m) = m XOR G(k). Along the same lines our new decryption function is D(k,c) = c XOR G(k). This is not perfectly secret, but it is reasonably secure. To redefine security in a way more applicable to this situation, let's further refine G.

We say G is predictable if there exists an efficent algorith A and a positive i less than or equal to n-1 such that the probabilty over a randomly chosen key K that we can predict that i+1th bit of G(k) given the first i bits of A(G(k)) is greater than half plus epsilon, for non-negligible epsilon. This is to say that if we have an algotithm A,  where knowing the result of the first i bits of the algorithm on the generated key, G(k), allows us to predict the i+1th bit of the generated key at greater than 50% accuracy, then G is predictable. if G is unpredictable if G is not predictable.

#### Note: *Non-negligible*
Epsilon is a function that maps the positive integers to the positive real numbers. Epsilon is non-negligible if there exists a d such that epsilon of lambda is greater than or equal to one over lambda to the power of d infinitely often. In practice this is roughly equivalent to epsilon being greater than one divided by two to the power of thirty.

### Attacks on Stream Ciphers
#### Key Reuse
We noted before without explaining, that keys for the one time pad can only be used once. To prove this, lets assume we intercepted two ciphertext messages made with the same key. How can we access the plaintext? The attack for this scenario is to xor the two ciphertexts. If we're working with the same key then we'll end up with our two messages xor-ed as the key will cancel out. While this will not reveal the seperate plaintexts it will produce a value, that due to the redundancies in english and ascii, we can obtain a result from, just like the sample ciphers in the last section. 

This may seem like an easy mistake to avoid, but stream ciphers have been used with repeated keys in ways that allowed exploits like this to exist in disk encryption and encrypting network traffic. To avoid this, it is now recommended to never use stream ciphers for disk encryption and it has become standard for networks to renegotiate keys each session. 

#### Manipulation
Stream ciphers often lack integrity, which is to say that an intercepted ciphertext can be modified in a way that is undetectable and leads to predictable changes to the plaintext. For example with the one time pad, xoring the ciphertext with a malicious edit, x, will lead the decode function to produce a "plaintext" that is really the plaintext xored with x.

---
### Example Stream Ciphers
#### RC4
RC4 was a stream cipher introduced in 1987. It was used in HTTPS and WEP. Unfortuantely there is a bias in the initial output that has been discovered which allows for related key attacks. Thus, RC4 is no longer in use today.

#### CSS
The CSS was a piece of hardware known as the Content Scramble System. The seed for CSS was only five bits, that was then fed through linear feedback shift registers and modular addition. A systematic and extremely quick attack exists to identify the key from ciphertext for CSS. At that point the message can be decrypted traditionally. 

#### eStream
eStream is a modern stream cipher that uses both a seed and a nonce. The nonce is non-repeating for any given key. The equation is then E(k, m; r) = m xor PRG(k;r). The PRG most commonly used is Salsa20, which has no known attack better than brute force. Despite this, we cannot prove that Salsa20 -- or any PRG is secure.

---

### Secure Pseudorandom Generators
We say a PRG is secure if for any efficient statistical test, A, the advantage of A, G is negligible. We define *statistical test* on the space {0,1} to the n as an algorithm, A, such that A(x) outputs 0 for not random or 1 for random. We define *advantage* A,G on A, a statistical test, and G, a PRG, as the absolute value of the probability that A(G(k))=1 for a random k from the seed space, minus the probability A(r)=1, with r being a random key from the full keyspace. There is currently no PRG that has been proven secure, although serious contenders are suspected to be secure and have not been proven insecure. We can note that its proven that a secure PRG if any are to be found, will be unpredictable. In the opposite direction, any unpredictable PRG will be secure. 

For future reference we should note that two distributions over {0,1} to the n are *computationally indistinguishable* if there exists an efficient statistical test, A, such that the difference between the probabilities that the statistical tests on elements of each distribution is one is less than negligible. 

---

### Semantic Security
When considering *semantic security* we define the advantage A,E on A, a statical test, and E, an encryption algorithm, as the difference between the probabilities of W<sub>a</sub> and W<sub>b</sub>. W<sub>a</sub> is the event that an experiment, a, equals 1. E is *semantically secure* if for all efficient A the semantic security advantage A,E is negligible. This means for all explicit m<sub>0</sub>, m<sub>1</sub> E(k, m<sub>0</sub>) is computationally indistinguishable from E(k, m<sub>1</sub>).

## Block Ciphers
### What are Block Ciphers?
Block ciphers are symmetric ciphers that operate on fixed size groups of bits, referred to as blocks. As an example 3DES can only take message text of 64 bits and requires a 168 bit key. This is in contrast to stream ciphers such as the one time pad, which can take messages of any size and requires no specific key length. This specificity is because block ciphers are bit on iteration. The key is expanded into multiple keys and then a round function, R(k<sub>i</sub>,m), is performed on each key and the message. 

In general, block ciphers in use today are faster than stream ciphers in use today.

Let's consider block ciphers more rigorously. We can consider block ciphers *Pseudorandom Functions*/*Pseudorandom Permuatations*. A PRF, f, defined over (K,X,Y) is a function that maps from KxX to Y such that there exists an efficient algorithm to evaluate f(k,x). A PRP, E, defined over (K,X) is a function that maps from KxX to X such that there exists an efficient deterministic algorithm to evaluate E(k,x), the function E(k, x) is one-to-one, and there exists an efficient inversion algorithm D(k,y). This is to say that E cannot be distinguished from a random permutation. Note that all PRPs are PRFs, but to be a PRP a PRF must also have X=Y and be invertible. 

Informally we can consider PRFs secure if f(k,x) is indistinguishable from a random function from X to Y and PRPs secure if E(k,x) is indistinguishable from a random one-to-one function from X to Y. 

### DES
DES, the Data Encryption Standard was established as the standard in 1976 and broken in 1997. It was widely deployed in the financial industry. The cipher is based on the idea of a Feistel Network. Given d funcions that maps {0,1} to the n onto itself, we want to build an invertible function from {0,1} to the 2n onto itself. We can define the network recursively where R<sub>i</sub> = f<sub>i</sub>(R<sub>i-1</sub>) xor L<sub>i-1</sub> and L<sub>i</sub> = R<sub>i-1</sub> where R<sub>0</sub> and L<sub>0</sub> are our input and R<sub>d</sub> and L<sub>d</sub> are our output. This is an invertible function and so our decryption version is this process in reverse order. If our functions are secure PRFs and we use independent keys, feistel networks of specific lengths have been proven secure PRPs. DES consists of a 16 round feistel network and a key expansion, taking our k out to 16 keys. The functions inside the feistel network are refered to as S-boxes, and then the final function on the network's results is the P-box. These functions need to be chosen carefully to avoid linearity. Chosing the functions at random would lead to an insecure cipher.    

### Attacks
As we noted earlier, DES is broken. The most significant method for breaking DES is the exhaustive search attack.

#### Exhaustive Search Attack
The exhaustive key search is a brute force attack to find the key given two message ciphertext pairs. The process is time consuming (potentially months of searching) or expensive (tens of thousands of dollars of computing power), but it has been repeatedly demonstrated as possible. The 56 bit key is too small. We can strengthen DES to prevent thus attack with 3DES, which triples the entire process. While no longer vulnerable to exhausive search, 3DES is slow, even for a block cipher. Why not 2DES? Doubling DES leaves the process vulnerable to a meet in the middle attack which would reduce the search time down to that of DES. DESX was also proposed to replace DES, but it too has been broken.

#### Side Channel Attacks
Rather than attacking the ciphertext or plaintext ciphertext pairs, attackers have been known to break block ciphers by observing the timing of encryption and decryption or the power consumption of the algorithms involved. Blocking this type of attack is fairly difficult and typically involves generating noise on the system and establishing protocols to maintain consistent computing times. 

#### Fault Attacks
A single error in the implementation of the last round of a block cipher is known to expose the secret key. 

#### Linear Attacks
As we noted when discussing DES, the presense of linearity in the rounding functions of the block cipher can make the entire thing linear. To show you intuitively what that means for security, an example of a linear cipher is a caesar cipher. Ciphers protect the message by making the relationship between the plaintext and ciphertext appear random. Preventing a linear relationship is crucial, but still extremely difficult, hence why attackers will still search for linear relationships and occassionally find them.

#### Quantum Attacks
Just as DES is has too small a keyspace to not be susceptible to brute force attacks, all block cipher attacks are susceptible to brute force when quantum computing is brought into play. Fortunately quantum computers are rare and expremely expensive to run, so only highly valuable decryptions would be worth months of computing time on a quantum computer.

### AES
AES is a Subs-Perm Network. Ten alternating Subs and Perm layers invoke the expanded keys to take our input to our output. The process involves byte substitution, row shifting, and column mixing. AES is currently implemented in browsers through Javascript and in Intel hardware. No reasonable attack is known to exist on AES (excluding quantum).

### Block Ciphers From PRGs
It is possible to build block ciphers from PRGs. We can expand PRGs into PRFs and the Luby-Rackoff Theorem expands these PRFs into PRPs. This is rarely used in practice as it is much slower than other methods. 

### Many Time Keys
So far, in this workshop, we've emphasised how using the same key more than once greatly decreases the security of our cipher. While this in true, in practice keys are commonly reused, because the process of securely exchanging new keys is so costly. A variety of processes have been developed to lessen the damage of many time keys.

#### Electronic Code Book
The most basic "strategy" for using a key multiple times, the Electronic Code Book makes no changes to the encryption process beyond saving results to reuse on duplicate messages. With ECB if we encrypt two identical messages we receive two identical ciphertexts. This doesn't seem like a huge problem, but in cases with large volumes of messages to incript, such as image encryption, clear patterns can emerge which give away a lot of data about the unencrypted image. In the same line, we would be able to easily modify the plaintext image, by adding a pattern to the ciphertext image. 

#### Random Algorithm
A strategy to combat this problem is to use randomised encryption and decryption algorithms. This is to say that if we have a message, m<sub>1</sub>, E would encrypt m<sub>1</sub> into c<sub>1</sub> or c<sub>2</sub>, etc, and D would decrypt both back into m<sub>1</sub>. While the theory behind this solution is elegant, it requires lengthening the ciphertext, which is unideal as we want to minimise the storage or transmission costs for our message as much as possible.

#### Nonce Use
We've already introduced the concept of nonces. By consistently varying our nonce we can avoid varying our key while dodging the identical results issue that arises with ECB. To avoid re-exchanging our nonce each transmission as we would a key, we can have our nonce be a counter or variation on a counter. Nonce based encryption is semantically secure under chosen plaintext attack. 

A *chosen plaintext attack* is when an attacker can encrypt as many messages of their choice as they want and then view their ciphertext. This is a reasonable privlege for us to grant our attacker if we are going to reuse our key, and we could see this occur in real life situations such as with access to a password protected encrypted disk. Our attacker cannot decrypt data without the password, but they can encrypt new messages of their chosing.  

#### CBC
In Cipher Block Chaining, each plaintext block is xored with the last block before being encrypted. To ensure uniqueness, a well chosen initial message, referred to as IV, is xor-ed with the first block. The ciphertext must then be padded to a multiple of ciphertext block. If no pad is needed we add a dummy block. Our IV must be unpredicatable. For added security we can implement a nonce with CBC, used with the first of the expanded keys.

#### CTR
CBC can be further refined by xor-ing a counter to IV or concatinating a nonce to IV. That including an easily predictable elment such as a counter will enhance security has had many skeptics, but in this case, no significant attacks have been found that cannot be blamed on poor choice of IV or the underlying functions, so the practice is widely accepted.

## Authenticated Encryption
### Motivation
We've introduced the idea of a chosen plaintext attack. While we can build cryptosystems relatively secure to it, they address eavesdropping rather than tampering. In a chosen plaintext scenario, an attacker with the additional ability to manipulate communication could simply replace aspects of the ciphertext with ones they encrypted themselves. To address this, Message Authentication Codes (MACS), have been developed to verify that messages are unaltered upon arrival, but on their own they provide not security from eavesdropping. To provide both tampering and eavesdropping protection we turn to authenticated encryption.

### Construction
An authenticated encryption system, (E,D), is a cipher when K x M maps to C and K x C maps to the union of M and a rejection state. A nonce can be integrated into this definition. The strength of authenticated encryption comes from the use of this rejection state: not only does the system provide semantic security under a chosen plaintext attack (eavesdropping protection)--it provides protection from tampering via *ciphertext integrity*, an attacker cannot create new ciphertexts that decrypt properly.

### Attacks
While authenticated encryption protects against eavesdropping and tampering, it does not protect against replay attacks. That is to say, an attacker who has intercepted a ciphertext and has access to the communication channel resend that entire message later, and the recipent would have no way of knowing it was sent by the attacker.

## Hash Functions
A *hash function* is a function that can be used to map data of arbitrary size to fixed size values. They are used in cryptographic schemes such as trapdoor permutations, as well as on their own such as SHA-256. Hash functions are not considered particularly secure and our concepts of security for them like "the random oracle" are extremely limited, however hash functions remain in use, because they are fast to use and many are unbroken.

## Trapdoor Permutation
### Public Key Encryption
We've seen from our experience with private key cryptography that securely exchanging a private key to begin communicating is as much a process as sending messages itself. For that reason, cryptographers pursued creating encryption algorithms that don't require a secret key. The premise behind these algorithms is a complete diversion from that of private key encryption: where private key encryption algorithms focus on maintaining invertability for decryption, public key encryption algorithms are typically easily computable one way, but extremely expensive to compute in the other direction. 

To begin the process, our first user generates our keys, PK and SK, and gives PK to the second user. Our second user then selects a random x and sends it encrypted with PK to the first user. A public key encryption system is a set of three algorithms, (G(), E(PK, m), D(SK, m)), where G() is a randomization algorithm that outputs a key pair (PK, SK), E(PK, m) is a randomized algorithm that outputs the ciphertext, c, and D(SK, c) is a deterministic algorithm that takes the cipher and output the plaintext or a rejection. 

Our previous definition of semantic security holds for this situation, however, unlike last time we need not differentiate between one time security and many time security, as our attacker can encrypt as many messages as they want themselves. This is why our encryption function must be randomised. Additionallu, unlike we noted in authenticated encryption, where we have an implied protection against chosen ciphertext attacks because the attacker cannot create new ciphertexts with public key encryption the attacker can create new ciphertexts so we need to directly require chosen ciphertext security to prevent tampering.

### Trapdoor Construction
A *trapdoor function* that maps X to Y is a set of three efficient algorithms, (G, F, F-inverse), where G() is a randomization algorithm that outputs a key pair (PK, SK), F(PK, x) is a deterministic algorithm that maps X to Y, and F-inverse(SK, y) is a deterministic algorithm that inverts F(PK, x). This system is secure if F is *one-way* which is to say F(PK, x) cannot be inverted without SK. More formally, the system is secure if for all efficient A, the advantage A,F which is the probability x=y, is less than negligible.  

To construct a public key encryption from a trapdoor function we need a secure TDF--(G, F, F-inverse) where F maps X to Y, (E<sub>s</sub>,D<sub>s</sub>)--a symmetric authenticated encryption over (K, M, c), and a hash function that maps X to K. Then for E(PK, m) we chose a random x from X, compute F(PK, x) which outputs y, compute H(x) to output k, run E<sub>s</sub>(k, m) to produce c, and then return y and c concatenated. To decrypt we have D(SK, (y,c)) we run F-inverse(SK, y) to get x, H(x) to output k, and D<sub>s</sub>(k,c) for m. We then output m. If our TDF is secure, our encryption algorithm is authenticated, and our hash is a random oracle, (G,E,D) is chosen ciphertext secure. 

### Trapdoor RSA
To introduce RSA we need to first establish some variables and notation. Let N=pq, where p, q are prime. Let Z<sub>N</sub> be the set of all non-negative integers less than N. Then (Z<sub>N</sub>)* is the set of all invertible elements of Z<sub>n</sub>. [A reminder, an element,x, is invertible in N if gcd(x, N)=1.] The number of elements in (Z<sub>N</sub>)* , phi,is N-p-q+1. For all x in (Z<sub>N</sub>)* , x to the phi-th power equals 1.

So consider a standard trapdoor permutation with these specifications. We use G() to choose random primes p,q, and e,d such that e * d = 1mod(phi). G outputs PK = (N, e) and SK = (N, d). RSA(x) is x to the eth power in Z<sub>N</sub> (our e, not the constant). Then F-inverse(SK, y) = y to the dth power, and y to the dth power = RSA(x) to the dth power = x to the (e * d)th power = x to the (k * phi)+1th power = x to the phith power to the kth power multiplied by x.  

RSA is built on the assumption that this is a one way permutation, because it should be much easier to find the product of two very large primes than it is to find which two very large primes are the sole factors of an extremely large number. It must be noted, however, that the trapdoor function requires all three algorithms for security. Versions of RSA without the hash and authenticated encryption algorithm are insecure.

### Attacks on RSA
#### PKCS1 - Bleichenbacher's attack
Daniel Bleichenbacher of Bell Labs is known for multiple attacks against RSA encryption and RSA signatures when implemented as SSL certificates. These attacks hold against the standard PKCS implementation of RSA. The attack compromises confidentiality, but is not easily used for tampering.

#### OAEP - Timing Attacks
Some history, the new function added to PKCS2, OAEP, was implemented with returns at different parts of the error process. This leaked why decrypting certain ciphertexts led to the fail state, a vulnerability that removed the security that fail state added.

#### Wiener's attack
Wiener's attack works on implementations that use a small key, d, to speed up the computation time of RSA. Algebraic evidence suggests that RSA decryption will never be securely sped up.

## Diffie-Hellman
### ElGamal Public-Key System
The ElGamal system relies on properties of finite cyclic groups. We start with G, a finite cyclic group of order n, and a hash function, H, that maps G-squared to K. Using this along with a symmetric authenticated encryption defined over (K,M,C), (E<sub>s</sub>,D<sub>s</sub>). We chose a random generator, g, in G, and a random a in Z<sub>n</sub>. To encrypt, given g, g to the a, and m, we select b at random from Z<sub>n</sub>. Then u is g to the power of b, and v is g to the power of a to the power of b. H(u,v) maps to k, and E<sub>s</sub>(k,m) maps to c. We output u and c. To decrypt, given a, u, and c, we set v to u to the power of a. Then H(u,v) maps to k, D<sub>s</sub>(k,c) maps to m, and we output m. 

### ElGamal Security
This hash variant of Diffie Hellman is semantically secure, but is not chosen ciphertext secure. To resolve this vulnerability, the process of twin ElGamal has been developed.
