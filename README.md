# Crypto Learnings
Personal notes created while reading about crypto

## Contents
- Goals of cryptosystems
    - Authentication
    - Confidentiality
    - Integrity
    - Non-repudiation
    - Plausible denaibility

- Attacks on cryptosystems
    - Ciphertext only
    - Known plaintext
    - Chosen plaintext
    - Chosen ciphertext

- Encryption
    - Key
    - Symmetric key
    - Asymmetric key

- Block Cipher
- Stream Cipher

- Block Cipher Modes
    - Confidentiality only
        - ECB (Electronic Codebook)
        - CBC (Cipher Block Chaining)
        - OFB (Output Feedback)
        - CTR (Counter)
    - Confidentiality & Authentication
        - CCM
        - CWC
        - GCM

- Generation of Initialization Vector (IV)
    - Static
    - Counter
    - Random
    - Nonce generated

- Order of encryption and authentication
    - Encrypt-then-MAC (EtM)
    - Encrypt-and-MAC (E&M)
    - MAC-then-Encrypt (MtE)

- Kerchoff's Principle
- Zero Knowledge Proofs
- One way functions
- Trust
- Error handling
- Replays and retries


## Goals of cryptosystems
Suppose Alice and Bob are best of friends and some random day, Alice wishes to send a message to Bob. 

#### Authentication
- Problem it solves: How does bob know for sure it came from Alice.

#### Confidentiality
- Problem it solves: How do Alice and Bob ensure that no one else can read the sent message.

#### Integrity
- Problem it solves: How does Bob ensure that the message wasn't tampered with in the way from Alice to him.

#### Non-repudiation
- Problem it solves: Bob has a friend Dave who doesn't believe Bob. How can Bob prove to Dave that it was Alice who sent him that particular message.

#### Plausible Denaibility
- Problem it solves: Alice wishes to send a message to Bob. Alice wants Bob to be able to verify that the message did in fact came from Alice. However, knowing the kind of people Bob hangs out with, Alice doesn't want Bob to be able to prove to anyone else that the message was sent by Alice.


## Attacks on cryptosystems
Keep in mind that the end goal for an attacker here is to not be able to read or modify data, but to get the pre-shared secret between the communicating parties

#### Ciphertext only attack
- The attacker has access to only the ciphertext data
- The is the most conservative attack model and in reality most attackers have access to much more than just ciphertext
- Even in this attack type, __traffic analysis__ can be done. For example, _when_ is the communication happening, _who_ is talking to _whom_, for _how long_ the communication is happening, _how much_ communication is happening etc.
- Example, Eve just has `G SLARTEDS AE` and nothing else

#### Known plaintext attack
- The attacker has access to some plaintext - ciphertext pairs
- This attack is also very realistic as real world communications are highly non-random
- Example, Eve has `G SLARTEDS AE` and knows it stands for `I JUST LANDED`

#### Chosen plaintext attack
- The attacker can choose the plaintext that has to be encrypted to ciphertext
- Example, Eve can choose a plaintext `ENCRYPT THIS` and get it encrypted by the communication channel `LAFJEJS STEAS`

#### Chosen ciphertext attack
- The attacker can select a ciphertext block and have it decrypted by the communication channel
- Example, Eve can instruct to have `LARAR ADE` decrypted and get the plain text value `EVIL ME`

## Encryption

#### Key
- Encryption works by transferring the responsibility of securing a large quantity of data to a small quantity of data.
- This small data is derived from the password/passphrase that we humans can remember.
- A key is of a constant length, say 128/192/256 bits for AES.

#### Key derivation function (KDF)
- Encryption functions require fixed length keys, but passphrases supplied by people are of arbitrary lengths, which is a problem.
- A key derivation function takes an arbitrary length passphrase and converts it into a constant length key that is then supplied to the encryption function

#### Symmetric Key
- Same key is used for encryption as well as decryption
- Think of a door lock, locking and unlocking requires the same key, both the times
- Significantly faster than asymmetric key encryption, as a result this is used for encrypting large quantities of data. 
- Requires less bits to provide security (AES provides sufficient security with 128 bits key)

#### Asymmetric Key (Public Key)
- Two keys (`k1 and k2`, often referred to as public and private keys) are used, one for encryption and the other for decryption
- If encryption is done with key1, decryption has to be done with k2 and vice versa.
- Slower than symmetric key encryption by about a thousand times, hence used only for encrypting small quantities of data like key during key exchange
- Requires a lot more bits to provide similar security (RSA uses an N of 2048 bits, where N = P x Q)

## Block Cipher
- Encrypts one block (say 128 bits) of plaintext into a similar sized ciphertext block. 
    - Consider a block cipher with block size 3 bits.
    - For every key K, we have a random mapping table between all possible plaintext and ciphertext blocks EG. [0,0,0] -> [1,0,1], [0,0,1] -> [0,1,0] and so on.
    - Ideally it should be random, such that no information about the key leaks through the ciphertext blocks
    - It is impractical to have precomputed mapping tables for each key and block size (lookup table for a 64 bit block cipher would be 150 million TB, for a single key)
- Collision is two distinct plaintext blocks (m and m') that compute to the same ciphertext block
- In general, an attacker can expect a collision after seeing 2^n/2 blocks of data, where n is the block size (for AES, this is 2^128/2 = 2^64 bits). This is a general problem called birthday paradox or birthday attack
- This is the reason Bruce Schneier recommends AES with 256 bit key rather than 128. 

## Stream Cipher
- Stream ciphers usually encrypt smaller quantities of data at a time, but still need to have a little block which can be from a bit to a couple of bytes.
- Essentially, the key is used to generate a key stream which is then xor'd with the plaintext to get a ciphertext stream.
- Some block cipher modes like OFB and CTR function like a stream cipher if the above definition is to be followed

## Block Cipher Modes
- A block cipher mode is an encrypting function used when more than just one block of data is to be processed.
- Think of it as a wrapper around the actual block cipher algorithm.
- Why is it required? Because if no cipher mode is used, a plaintext ABC will encrypt to the same ciphertext XYZ for a single key.
- How is that a problem? Given our everyday communications are not truly random (for example, packet headers, misc data that repeats in a typical internet exchange (say the sender's ID in a whatsapp group chat)), we do not want to leak the information that some particular data is being repeated.
- Block cipher modes are divided into the ones providing confidentiality only and the ones providing confidentiality and authentication

### Block Ciphers providing confidentiality only

#### EBC (Electronic CodeBook)
- _Electronic Codebook_ mode is the simplest to understand of all block cipher modes.
- Basically, `C[i] = E(K, P[i])` for all i=1...N blocks of data.
- Insecure, should never be used.

#### CBC (Cipher Block Chaining)
- _Cipher Block Chaining_ is what it sounds like, we chain the ciphers in some way.
-  Previous block's ciphertext is xor'd with the current plaintext block before encryption.
- `C[i] = E(K, C[i-1] ⊕ P[i])` where ⊕ represents the xor or exclusive-or operation.
- The issue here is getting the first ciphertext block. Generally, an Initialization Vector. 
- Initialization Vectors may be static (Same IV used every time), counter (Incremented upon every use) or random (randomly generated upon every use). Details in initialization vector section.

#### OFB (Output Feedback)
- _Output Feedback_ mode uses the block cipher to generate a key stream and ⊕s the key stream with the message
- Works like a stream cipher, as in a stream of pseudo random bits is ⊕'d with the plaintext to generate ciphertext
- `K[i] = E(K, K[i-1])` for all i=1...N and ciphertext block `C[i] = K[i] ⊕ P[i]`
- Initialization vector is used for the very first block.
- Decryption is the exact same as encryption.
- No padding is required.
- IV should not be repeated

#### CTR (Counter)
- _Counter_ mode is similar to OFB, in that it is a stream cipher mode
- Defined by `K[i] = E(K, Nonce + i)` for i=1...N and `C[i] = P[i] ⊕ K[i]` where Nonce stands for _number used once_ and it is just that, and _i_ is the counter value.
- No padding is required
- Nonce should never be repeated

### Block Ciphers providing authentication & confidentiality

#### CCM
#### CWC
#### GCM

## Order of encryption and authentication

#### Encrypt then MAC (EtM)
- Encrypt the plain text, then authenticate the ciphertext
- Applying MAC to weak encryption scheme fixes it
- More efficient in discarding bogus messages as overhead to decrypt bogus messages is avoided

#### Encrypt & MAC (E&M)
- Encryption and MAC can happen in parallel
- MAC is that of plaintext, hence any weakness in MAC algorithm can leak information about the plaintext itself.

#### MAC then Encrypt (MtE)
- MAC the plaintext and append it to the plaintext
- Encrypt the plaintext and MAC
- Does not expose the MAC value to Eve as both the plaintext and the MAC are encrypted
- Protects authentication above encryption
- In general, being able to modify messages is more damaging than being able to read messages (Cryptography Engineering [7.2]) 

## Generation of Initialization Vector (IV)

#### Static IV
- A static value is used as the first block of encryption mode
- Insecure as IVs are sent in plaintext,
- Same problem as with EBC

#### Counter IV
- Not very secure as incremental values (very similar) can occasionally cancel out differences in plaintext (again, very similar) and generate identical ciphertext blocks

#### Random IV
- Most secure
- The recipient has the know this value in advance
- Standard solution is to send the first block of the message as the IV (`C[0]`)
- Disadvantage is that the ciphertext is one block longer than the plaintext which is a problem for small messages

#### Nonce Generated IV
- Nonce stands for number used once, is a random number that both the parties can calculate somehow.
-  This number is then concatenated with additional information already available
- The IV is generated by encrypting this nonce
- Only requirement is that the nonce should never be reused

## Kerchoff's Principle
The security of a cryptosystem should depend only on the key and not on anything else. (assume everything else is known to an adversary)

## Zero Knowledge Proofs
- A method to prove the possession of some information without giving away the information.
- Should satisfy completeness, soundness and zero-knowledge properties

## One Way Functions (Hash functions)
- Take an arbitrary sized input and map it to an output of fixed size. 
- Since the input is arbitrary sized, an infinite number of collision are theoretically possible. Security is in an attacker not being able to find two messages m and m' such that H(m) == H(m') in reasonable time.
- Most hash function output a 128-1024 bit sized hash.
- Most hash functions are iterative in their implementation.
- Used for integrity check; in HMAC for authentication of message;
- SHA1 is broken.
- SHA2 with 256 and 512 bits is vulnerable to length extension attacks, and SHA2 224 and 384 are therefore more secure.
- SHA3, the newest in the SHA family, is the recommended by experts.

## Trust
- Ultimate basis for all human interaction
- There are several sources of trust
    - Ethics
    - Reputation
    - Physical threat
    - Mutually assured destruction
- Trust happens when one party knows the incentives of the other party to behave properly
- Incentives fail when dealing with irrational people as they cannot be trusted to act in their best interests
- "Do you trust him?" is an incomplete question. "Do you trust him with X?" is the right question as trust isn't white or black but grey. <- degrees of trust
- Risk, which is the converse of trust, is usually measured in business.
- Understand what incentives drive people and then create your protocols accordingly
- The function of cryptographic protocols is to minimize the number of people who need to trust each other and the amount of trust they need to have

## Secure Software Implementation
- Problem: No one knows how to write correct software
- Reason: 
    - No clear specification
        - S/W must have requirements, functional specification and implementation design
    - Test and fix approach does not produce a correct program. It produces a program that works fine in most common situations.
        - Testing can only show the presence of bugs, not absence.
        - If you find a bug, first implement a test that will detect the bug. 
        - When a bug is found, think about what caused it
    - Lax attitude towards software bugs

## Error Handling
- Errors can give away lot of useful information to an attacker
- Whenever possible, don't send an error response
- Watch out for interaction between errors and timing attacks
- If an error has to be returned, make it as generic as possible. 'An error occurred' will do.

## Replays and Retries
- Replay occurs when Eve sends an earlier message of Alice to Bob, she 'replays' a message.
- Retry occurs when Alice doesn't receive an acknowledgement for an earlier message she send, and she sends it again, and Bob receives two similar messages.
- Guidelines to deal with replays and retries
    - If the message is future dated, ignore it
    - If the message is from past, check if the message is same. If it is same, respond in the exact similar way
    - If the message is different, then either the previous message was bogus, or the current one is bogus. This is an indication of an attack and should be treated as protocol error.
    - If the current message is older than the message you've already received, check if it is identical. If yes, ignore. If no, this can be an attack and should be treated as protocol error.
- It is impossible to know whether the last message in the protocol arrived. 