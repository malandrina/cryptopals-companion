# Applied Cryptography for the Impatient: A Cryptopals Companion

_Updated 8 February 2024_

---

## Introduction

_Applied Cryptography for the Impatient_ is an index of technical terms, concepts and algorithms referenced in [Cryptopals](https://cryptopals.com) Challenges 1 - 28.

- Concise definitions from credible sources
- Links to more detailed references online
- No code and no Cryptopals spoilers

It is very much👷‍🚧 under construction 🚧👷‍. Planned improvements can be viewed [here](https://github.com/malandrina/cryptopals-companion/issues).

## Contributing

Did you notice a typo, a factual error, an omission or an index entry that would be improved with a link to your favorite resource? [Open an issue!](https://github.com/malandrina/cryptopals-companion/issues)

---

## Index
### Bitwise operations
Further reading:
  - [Bitwise operation, Wikipedia](https://en.wikipedia.org/wiki/Bitwise_operation)

#### XOR

Further reading:
  - [Exclusive or, Wikipedia](https://en.wikipedia.org/wiki/Exclusive_or)
  - [Why is XOR used in cryptography?, Stack Overflow](https://stackoverflow.com/questions/1379952/why-is-xor-used-in-cryptography)

### Buffer

> A buffer is a storage in physical memory used to temporarily store data while it is being transferred from one place to another.

"Buffer". [MDN Web Docs Glossary](https://developer.mozilla.org/en-US/docs/Glossary/Buffer). Accessed 1 February 2024.

  See also: [Character encoding](?id=character-encoding)

  Further reading:
  - [Data buffer, Wikipedia](https://en.wikipedia.org/wiki/Data_buffer)

---

### Character encoding

> An encoding defines a mapping between bytes and text. A sequence of bytes allows for different textual interpretations. By specifying a particular encoding (such as UTF-8), we specify how the sequence of bytes is to be interpreted.

"Character encoding." [MDN Web Docs Glossary](https://developer.mozilla.org/en-US/docs/Glossary/Character_encoding). Accessed 1 February 2024.

#### ASCII

> ASCII (American Standard Code for Information Interchange) is a character encoding standard of 128 7-bit used by computers for converting letters, numbers, punctuation, and control codes into digital form.

"ASCII". [MDN Web Docs Glossary](https://developer.mozilla.org/en-US/docs/Glossary/ASCII). Accessed 1 February 2024.

#### Base16

A binary-to-text encoding scheme similar to Base64.

See also: [Hexadecimal](?id=hexadecimal), [Base64](?id=base64)

#### Base64

> Base64 is a group of similar binary-to-text encoding schemes that represent binary data in an ASCII string format by translating it into a radix-64 representation.

"Base64". [MDN Web Docs Glossary](https://developer.mozilla.org/en-US/docs/Glossary/Base64). Accessed 1 February 2024.

#### UTF-8

> UTF-8 (UCS Transformation Format 8) is the World Wide Web's most common character encoding. Each character is represented by one to four bytes. UTF-8 is backward-compatible with ASCII and can represent any standard Unicode character.

"UTF-8". [MDN Web Docs Glossary](https://developer.mozilla.org/en-US/docs/Glossary/UTF-8). Accessed 1 February 2024.

---

### Cipher

> A **cryptographic algorithm**, also called a **cipher**, is the mathematical function used for encryption and decryption. (Generally, there are two related functions: one for encryption and the other for decryption.)

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 2.

> There are two basic types of symmetric algorithms: block ciphers and stream ciphers. **Block ciphers** operate on blocks of plaintext and ciphertext-- usually of 64 bits but sometimes longer. **Stream ciphers** operate on streams of plaintext and ciphertext one bit or byte (sometimes even one 32-bit word) at a time. With a block cipher, the same plaintext block will always encrypt to the same ciphertext block, using the same key. With a stream cipher, the same plaintext bit or byte will encrypt to a different bit or byte every time it is encrypted.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 189.

#### Stream cipher

> Stream ciphers convert plaintext to ciphertext 1 bit at a time. ... .A **keystream generator** (sometimes called a running-key generator) outputs a stream of bits: _k1, k2_, _k3_, ..., _ki_. This keystream (sometimes called a running key) is XORed with a stream of plaintext bits, _p1_, _p2_, _p3_, ..., _pi_, to produce the stream of ciphertext bits. At the decryption end, the ciphertext bits are XORed with an identical keystream to recover the plaintext bits.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 197.

---

### Ciphertext

> A message is **plaintext** (sometimes called cleartext). The process of disguising a message in such a way as to hide its substance is **encryption**. An encrypted message is **ciphertext**. The process of turning ciphertext back into plaintext is **decryption**.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 1.

---

### Cryptographic attacks
#### Bit-flipping

**What the attacker knows**
- The format and language of the plaintext, e.g. ASCII-encoded English
- The method of encryption: Stream or block cipher
- The length and content of attacker-controlled plaintext
- The length and content of ciphertexts derived from attacker-controlled plaintexts

**What the attacker must learn**
- The bytes that must be substituted for a subset of the ciphertext such that decrypting the modified ciphertext produces the desired plaintext

See also: [Cryptographic modes - CBC](?id=cbc), [Encryption oracle](?id=encryption-oracle)

Further reading:
  - [Attacking CBC Mode Encryption: Bit Flipping, Zhang Zeyu, Medium](https://zhangzeyu2001.medium.com/attacking-cbc-mode-bit-flipping-7e0a1c185511)
  - [Bit-flipping attack, Wikipedia](https://en.wikipedia.org/wiki/Bit-flipping_attack)
  - [Cryptopals Set 2 Challenge 16](https://cryptopals.com/sets/2/challenges/16)

---
#### Break repeating-key XOR

**What the attacker knows**

- The format and language of the plaintext, e.g. ASCII-encoded English
- The method of encryption: repeating-key XOR
- The data type of the key parts: `u8`
- The length, content and encoding scheme of the ciphertext

**What the attacker must learn**

- The length of the key
- The key
- The content of the plaintext

**The attack**

Decode the ciphertext and determine the length of the key `KEYSIZE`: For `2..=n`, where `n` is the maximum likely key length, split the decoded ciphertext into blocks of `n` length and compute the average Hamming distance between the blocks. The `n` with the lowest average Hamming distance is the `KEYSIZE`.

Transpose the decoded ciphertext. Split the decoded transposed ciphertext into blocks of `KEYSIZE` length and recover the key `KEY`: For each block, iterate over each possible key value `k` in the range `0u8..255u8`:
  - Construct a candidate key: candidate `k` repeated `KEYSIZE` times.
  - XOR the block and candidate key.
  - ASCII-or UTF-8-encode the result.
  - Score the encoded result as English plaintext by comparing character frequencies in the encoded result to character frequencies in written English.
  - Identify the `k` value with the best score and add it to `KEY`.

Finally, construct a repeating key `REPEATING_KEY` using `KEY`. XOR `REPEATING_KEY` with the decoded ciphertext to decrypt.

See also: [Brute force](?id=brute-force), [Hamming distance](?id=hamming-distance)

Further reading:
  - [Cryptopals Set 1 Challenge 6](https://cryptopals.com/sets/1/challenges/6)
  - [Frequency analysis, Wikipedia](https://en.wikipedia.org/wiki/Frequency_analysis)
  - [Letter frequency, Wikipedia](https://en.wikipedia.org/wiki/Letter_frequency)
  - [Transpose, Wikipedia](https://en.wikipedia.org/wiki/Transpose)
---

#### Brute force

**What the attacker knows**
- The range of possible keys
- The likeliest keys
- The amount of time and computational power needed to attempt decryption with each possible key

**What the attacker must learn**
- The correct key

**The attack**

Iterate over possible keys. For each key, starting with those most likely to be correct, attempt to decrypt the ciphertext and evaluate the result.

Further reading:
  - [Brute-force attack, Wikipedia](https://en.wikipedia.org/wiki/Brute-force_attack)

---

#### Byte-at-a-time

**What the attacker knows**

- The format and language of the target plaintext, i.e. ASCII-encoded English
- The mode of encryption: ECB
- The length and content of the target ciphertext
- The length and content of attacker-controlled plaintexts
- The length and content of ciphertexts derived from attacker-controlled
  plaintexts

**What the attacker must learn**

- The length of the key
- The number of bytes the encryption oracle prepends to the plaintext before encryption (the _prefix_)
- The length of the target plaintext
- The content of the target plaintext

**The attack**

Determine the length of the key `KEYSIZE`, the prefix length `PREFIX_LEN` and the length of the target plaintext `P` with repeated calls to the encryption oracle with incrementally larger chosen plaintexts.

For `i` in `0..LEN(P)`, perform a brute-force attack to recover the value of `P[i]`: For `n` in possible values `0u8..255u8`, construct a plaintext comprising previously recovered bytes and `n`, encrypt the plaintext and add the resulting ciphertext to a lookup table. `P[i]` is equal to the `n` value corresponding to the ciphertext that matches `C`.

See also: [Cryptographic attacks - Brute force](?id=brute-force), [Cryptographic modes - ECB](?id=ecb)

Further reading:
  - [Byte by Byte ECB Decryption, Node Security, 2021](https://node-security.com/posts/cryptography-byte-by-byte-ecb-decryption/)
  - [Cryptopals Set 2 Challenge 12](https://cryptopals.com/sets/2/challenges/12)
  - [Cryptopals Set 2 Challenge 14](https://cryptopals.com/sets/2/challenges/14)
---

#### Chosen-plaintext

> The cryptanalyst not only has access to the ciphertext and associated plaintext for several messages, but he also chooses the plaintext that gets encrypted. This is more powerful than a known-plaintext attack because the cryptanalyst can choose specific plaintext blocks to encrypt, ones that might yield more information about the key. His job is to deduce the key (or keys) used to encrypt the messages or an algorithm to decrypt any new messages encrypted with the same key (or keys).

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 6.

---

#### Clone MT19937 from output

**What the attacker knows**

- The implementation details of MT19937

**What the attacker must learn**

- The first 624 numbers generated by the target instance of MT19937
- Subsequent numbers generated by the target instance of MT19937

**The attack**

Write a function that reverses MT19937's tempering function. Using the instance of MT19937 under attack, generate 624 random numbers and untemper them.

Instantiate a new MT19937 PRNG and set its internal state equal to the untempered 624 numbers.

See also: [Mersenne Twister](?id=mersenne-twister), [Pseudo-random number generators](?id=pseudo-random-number-generators), [Random number generators](?id=random-number-generators)

Further reading:
  - [Cryptopals Set 3 Challenge 23](https://cryptopals.com/sets/3/challenges/23)
  - [Matsumoto, M. and Nishimura, T., Mersenne Twister: A 623-Dimensionally Equidistributed Uniform Pseudo-Random Number generator, 1998 (PDF)](https://dl.acm.org/doi/pdf/10.1145/272991.272995)
  - [Wikipedia](https://en.wikipedia.org/wiki/Mersenne_Twister)

---

#### Crack MT19937 seed

**What the attacker knows**
- The implementation details of MT19937
- The fact that PRNGs are often seeded with the current time
- The probable range of possible seed values

**What the attacker must learn**
- The seed value

**The attack**

Using the instance of MT19937 under attack, generate a random number note the current time `c`. For `s`, the most probable smallest possible seed value, to the current time `c`, instantiate a new MT19937 with the candidate seed and generate a random number. Determine whether the candidate seed is the original seed by comparing the random number to the original random number: If they are the same, the candidate seed is correct.

See also: [Mersenne Twister](?id=mersenne-twister), [Pseudo-random number generators](?id=pseudo-random-number-generators), [Random number generators](?id=random-number-generators)

Further reading:
  - [Cryptopals Set 3 Challenge 22](https://cryptopals.com/sets/3/challenges/22)
  - [Matsumoto, M. and Nishimura, T., Mersenne Twister: A 623-Dimensionally Equidistributed Uniform Pseudo-Random Number generator, 1998 (PDF)](https://dl.acm.org/doi/pdf/10.1145/272991.272995)
  - [Wikipedia](https://en.wikipedia.org/wiki/Mersenne_Twister)

#### Cut-and-paste

**What the attacker knows**
- The format and language of the target plaintext, i.e. ASCII-encoded English
- The mode of encryption: ECB
- The length and content of attacker-controlled plaintexts
- The length and content of ciphertexts derived from attacker-controlled plaintexts

**What the attacker must learn**
- The bytes that must be substituted for a subset of the ciphertext such that decrypting the modified ciphertext produces the desired plaintext

See also: [Cryptographic attacks - Chosen plaintext](?id=chosen-plaintext), [Cryptographic modes - ECB](?id=ecb)

Further reading:
  - [Cryptopals Set 2 Challenge 13](https://cryptopals.com/sets/2/challenges/13)
  - [ECB Cut and Paste Attack, Bernardo de Araujo, July 2021](https://bernardoamc.com/ecb-cut-paste-attack/)
---

### Cryptographic modes

> A cryptographic **mode** usually combines the basic cipher, some sort of feedback, and some simple operations. The operations are simple because the security is a function of the underlying cipher and not the mode. Even more strongly, the cipher mode should not compromise the security of the underlying algorithm.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 189.

See also: [Cipher](?id=cipher)

#### CBC

> Chaining adds a **feedback** mechanism to a block cipher. The results of the encryption of previous blocks are fed back into the encryption of the current block. In other words, each block is used to modify the encryption of the next block. Each ciphertext block is dependent not just on the plaintext block that generated it but on all the previous plaintext blocks. In **cipher block chaining (CBC)** mode, the plaintext is XORed with the previous ciphertext block before it is encrypted. After a plaintext block is encrypted, the resulting ciphertext is also stored in a feedback register to become the next input to the encrypting routine. The resulting ciphertext is again stored in the feedback register, to be XORed with the next plaintext block and so on until the end of the message. The encryption of each block depends on all the previous blocks.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 193.

Further reading:
  - [Block cipher mode of operation, Wikipedia](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_block_chaining_(CBC))

#### CTR
> Block ciphers in **counter mode** use sequence numbers as the input to the algorithm. Instead of using the output of the encryption algorithm to fill the register, the input to the register is a counter. After each block encryption, the counter increments by some constant, typically one.

> Stream ciphers in counter mode have simple next-state functions and complicated output functions dependent on the key. ... The next-state function can be something as simple as a counter, adding one to the previous state.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 205-206.

Further reading:
  - [Block cipher modes of operation, Wikipedia](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Counter_(CTR))

#### ECB

> **Electronic codebook (ECB)** is the most obvious way to use a block cipher: A block of plaintext encrypts into a block of ciphertext. Since the text always encrypts to the same block of ciphertext, it is theoretically possible to create a code book of plaintexts and corresponding ciphertexts. However, if the block size is 64 bits, the code book will have 2 ** 64 entries -- much too large to pre-compute and store.

Schneier, Bruce, *Applied Cryptography, Second Edition: Protocols, Algorithms and Source Code in C*, (Indianapolis: John Wiley & Sons, 1996), 190.

Further reading:
  - [Block cipher mode of operation, Wikipedia](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_(ECB))

### Encryption oracle
### Encryption standards
#### AES

Advanced Encryption Standard. U.S. National Institute of Standards and Technology specification for the encryption of data using a symmetric key algorithm.

Further reading:
  - [Advanced Encryption Standard, Wikipedia](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)

#### DES

Data Encryption Standard. Preceded AES.

Further reading:
  - [Data Encryption Standard, Wikipedia](https://en.wikipedia.org/wiki/Data_Encryption_Standard)

### Frequency analysis

### Hamming distance

Further reading:
  - [Wikipedia](https://en.wikipedia.org/wiki/Hamming_distance)

---

### Hexadecimal

A positional number system using 16 as its base.

See also: [Base16](?id=base16)

Further reading:
 - Strickland, Lloyd and Jones, Owain Daniel, F Things You (Probably) Didn't Know About Hexadecimal, The Mathematical Intelligencer 18 August 2022, Volume 45 pages 126-130 (2023). https://link.springer.com/article/10.1007/s00283-022-10206-w#Fn1.

---

### Keystream
See: [Stream cipher](?id=stream-cipher)

### Nonce

> A random or non-repeating value that is included in data exchanged by a protocol, usually for the purpose of guaranteeing the transmittal of live data rather than replayed data, thus detecting and protecting against replay attacks.

"Nonce". [NIST Computer Security Resource Center Glossary](https://csrc.nist.gov/glossary/term/nonce). Accessed 6 February 2024.

### Padding

> Oftentimes messages are not an integer multiple of the block size and hence need to be *padded*. The *padding* is typically a map that takes the last partial block of the message ... and maps it into a full block ... The map needs to be invertible which in particular means that if the message is already an integer multiple of the block size we will need to add an extra block.

Boaz, Barak, [*An Intensive Introduction to Cryptography*](https://intensecrypto.org/public/lec_06_CCA.html#padding-chopping-and-their-pitfalls-the-buffer-overflow-of-cryptography). Accessed 2 February 2024.

Further reading:
  - [Padding (cryptography), Wikipedia](https://en.wikipedia.org/wiki/Padding_(cryptography))

#### PKCS#7

Further reading:
  - [PKCS padding method, IBM](https://www.ibm.com/docs/en/zos/3.1.0?topic=rules-pkcs-padding-method)

### Pseudo-random number generators
#### Mersenne Twister
### Random number generators
### Vigenère cipher

Polyalphabetic substitution cipher invented by Giovan Battista Bellaso in the 16th century, misattributed to Blaise de Vigenère.

Further reading:
  - [Vigenère cipher, Wikipedia](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)
---

### Word
  >*Word* - Any type that is size of a pointer. This corresponds to the width of the CPU's registers.

  McNamara, Tim, *Rust in Action*, (New York: Manning Publications Co., 2021), 202.

  Further reading:
  - [Word (compute architecture), Wikipedia](https://en.wikipedia.org/wiki/Word_(computer_architecture))
---
