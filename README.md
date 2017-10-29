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
    - Choosen plaintext
    - Choosen ciphertext

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

## Goals of cryptosystems
Suppose Alice and Bob are best of friends and some random day, Alice wishes to send a message to Bob. 

#### Authentication
- Problem it solves: How does bob know for sure it came from Alice.

#### Confidentiality
- Problem it solves: How do Alice and Bob ensure that no one else can read the sent message.

### Integrity
- Problem it solves: How does Bob ensure that the message wasn't tampered with in the way from Alice to him.

### Non-repudiation
- Problem it solves: Bob has a friend Dave who doesn't believe Bob. How can Bob prove to Dave that it was Alice who sent him that particular message.

### Plausible Denialibility
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

#### Choosen plaintext attack
- The attacker can choose the plaintext that has to be encrypted to ciphertext
- Example, Eve can choose a plaintext `ENCRYPT THIS` and get it encrypted by the communication channel `LAFJEJS STEAS`

#### Choosen ciphertext attack
- The attacker can select a ciphertext block and have it decrypted by the communication channel
- Example, Eve can instruct to have `LARAR ADE` decrypted and get the plain text value `EVIL ME`

## Block Cipher Modes
- A block cipher mode is an encrypting function used when more than just one block of data is to be processed.
- Think of it as a wrapper around the actual block cipher alogrithm.
- Why is it required? Because if no cipher mode is used, a plaintext ABC will encrypt to the same ciphertext XYZ for a single key.
- How is that a problem? Given our everyday communications are not truely random (for example, packet headers, misc data that repeats in a typical internet exchange (say the sender's ID in a whatsapp group chat)), we do not want to leak the information that some particular data is being repeated.
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
- Initialization Vectors may be static (Same IV used everytime), counter (Incremented upon every use) or random (randomly generated upon every use). Details in initialization vector section.

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
- MAC the plaintext and appeand it to the plaintext
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
