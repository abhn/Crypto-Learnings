# Crypto Learnings
Personal notes created while reading about crypto

## Contents
- Block Cipher Modes


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
_Output Feedback
