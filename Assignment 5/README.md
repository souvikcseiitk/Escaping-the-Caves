# AES Attack

## Commands used in the game
<pre> 

To reach the cipher text, we used  the command in the following sequence :      
go -> wave -> dive -> go -> read -> password

</pre>
## Analysis

<pre>
<strong>Breaking the Encoding</strong>

Our first task was to figure out the encoding which was being used to convert the
input string into binary blocks. After analysing the output on several inputs.
We observed that all the output letters range from f to u, which corresponds to 
16 letters so we thought that encodings may be related with hex-base systems. When
we observe two letters at a time (i.e. in pairs), we observe that all the pairs 
are from “ff” to “mu”, which is a total of 128 pairs. Since we are using the 
F_{128} field here , each pair of characters must encode to a number from 0 to 127.
Thus, we mapped ff to 0 , fe to 1 ,... , mu to 127. i.e., mu = 7*16 + 15 = 127.

<strong>Observation</strong>

Consider the following plaintext-ciphertext pairs :
                                 
ff ff ff ff ff ff ff ff	:	ff ff ff ff ff ff ff ff
ff ff ff ff ff ff ff fg	:	ff ff ff ff ff ff ff js
ff ff ff ff ff ff fg ff	:	ff ff ff ff ff ff is lk
ff ff ff ff ff fg ff ff	:	ff ff ff ff ff fm hh ko
ff ff ff ff gf ff ff ff	:	ff ff ff ff ks ku ft is
ff ff ff gf ff ff ff ff	:	ff ff ff mi ii lo mr mh
ff fg ff ff ff ff ff ff	:	ff lq kj is jq hf gk fh


hi hi hi hi hi hi hi hi	:	ih ih lq kp gq hp hh fj
hi hi hi hi hi hi hi hm	:	ih ih lq kp gq hp hh jq
hi hi hi hi hi hi mi hi	:	ih ih lq kp gq hp mp mo
hi hi hi hi hi mi hi hi	:	ih ih lq kp gq il jj jn
hi hi mi hi hi hi hi hi	:	ih ih km go gm ir gm kh


We observed here that the ith block(pair) of plaintext only affects the 
corresponding i^{th} block of ciphertext and the blocks after that. 
Or in other terms, the j^{th} block of the ciphertexts depends on  all
i blocks of plaintext where i <= j. So the 1st block of the 
ciphertext depends only on the 1st block of the plaintext, the 
2nd block of the ciphertext depends only on the first two blocks 
of the plaintext, and so on.


<strong>Finding the first block of decrypted password</strong>


The above observation makes it straightforward to find the decrypted password.
Note that the first block of encrypted password (i.e. ciphertext) depends only
on the first block of decrypted password (i.e. plaintext). Also, the 
correspondence must be one-one, otherwise one ciphertext will correspond
to multiple plaintext which cannot happen. 

So, we can create a group of 128 plaintext blocks that have all blocks set to 
zero, except the first block which takes the values 0 to 127. We know that the
first block of the encrypted password is “lh” , and depends only on the first
block of the plaintext. Now, one of the plaintexts of the group must result in
the ciphertext with the first block as “lh”. The first block of this plaintext
is then the first block of the decrypted password. And the corresponding first
block of plaintext obtained was “mj”.


<strong>Finding subsequent blocks of decrypted password </strong>


Consider the second block. The second block of the encrypted password(i.e ciphertext)
depends on the first two blocks of the plaintext. We already know the first block, and
just need to loop over the 128 possible values of the second block. So, we create a group
of plaintext which have the first block equal to the value found in the previous section,
the second block takes the 128 possible values in $$F_{128}$$, and the rest of the blocks
are set to zero. Again, the second block also holds a one-to-one correspondence between
plaintext and ciphertext, thus we will get a unique value for the second block. Similarly,
fixing the values of blocks and looping over the next block, we can get the value of each
block easily. Repeating this process for both the blocks of the encrypted password, we get
the full decrypted password.

Each pair requires 128 iterations to get the corresponding pair of plaintext. So,to get the
plaintext of one block (i.e. 8 pairs) we need 128 x 8 iterations. 

To find each block of decrypted password as mentioned above we used script.sh. In this firstly
we generated  all 128 plaintext in plaintext.txt to find a specific pair using generate_input.cpp
and then find all ciphertext in cipher.txt corresponding to each plaintext using input.cpp and
read.cpp. We get,

encrypted password  :    lhisinlijliuikmnlumtishqlrfnhfft
decrypted password  :    mjmkmhmjljmmltltlkmpifififififif

<strong>Finding the answer</strong>

We entered the above decrypted password in the terminal , the next level does not open up -
only the above encrypted password is shown. Which means that there is another decoding to 
be applied on this 32-character decrypted password. 
Now notice that each pair of characters is from ff - mu so we can map this to 0 - 127,
as we did in the start. So after mapping each pair, we get 

decrypted password  :    mj mk mh mj lj mm lt lt lk mp if if if if if if

mapped password     :    116 117 114 116 100 119 110 110 101 122 48 48 48 48 48 48 


As the above number lies between 48 and 127 we thought that these could be ASCII codes. 
We converted these numbers into characters, we get 

mapped password     :    116 117 114 116 100 119 110 110 101 122 48 48 48 48 48 48 

Corresponding ASCII :     t u r t d w n n e z 0 0 0 0 0 0

We tried turtdwnnez000000 but this was not the password. So, we thought 0’s were just 
used for padding similar to level 4.
So using turtdwnnezas final command , we cleared the level.


</pre>

---

# Laymen's Language

## **Encryption (Encoding the Plaintext)**

The goal is to convert **plaintext** (human-readable text) into **ciphertext** (encoded form), which appears random but can be decoded with the proper rules.

### 1. **Pair the Characters**
   - Take the plaintext (e.g., `"lkmpifififififff"`) and split it into pairs of two characters each:
     ```plaintext
     lk | mp | if | if | if | if | ff
     ```

### 2. **Map Each Pair Using F₁₂₈**
   - Use a mapping system (based on the finite field F₁₂₈) to convert each pair into a number between 0 and 127:
     - Each pair is treated as a "row-column" combination in a grid of characters from `'f'` to `'u'`.
     - For example:
       - `'l'` is the 6th character after `'f'`, and `'k'` is the 5th character after `'f'`.
       - Map `'lk'` as: `16 × 6 + 5 = 101`.

### 3. **Convert Back to Characters**
   - Convert the number (e.g., `101`) back to another pair of encoded characters (`mj`, `mk`, etc.) using the same row-column mapping:
     - For `101`: `row = 6 (l)`, `column = 5 (k)` → pair = `mj`.

### 4. **Repeat for All Pairs**
   - Perform this for all pairs, creating the encoded ciphertext:
     ```plaintext
     mj | mk | mh | mj ...
     ```

### 5. **Add Dependencies**
   - Each layer of encryption introduces dependencies where the ciphertext blocks depend on all previous plaintext blocks. This makes the ciphertext harder to decode without the correct process.

---

## **Decryption (Decoding the Ciphertext)**

The goal is to reverse the encryption process to convert the ciphertext back to plaintext.

### 1. **Break the Ciphertext into Pairs**
   - Take the encoded ciphertext (`"mjmkmhmj..."`) and split it into pairs:
     ```ciphertext
     mj | mk | mh | mj ...
     ```

### 2. **Map Each Pair Back Using F₁₂₈**
   - Reverse the mapping by treating each pair (`mj`, `mk`, etc.) as a "row-column" combination:
     - For `mj`:
       - `'m'` is the 7th character after `'f'` → `row = 7`.
       - `'j'` is the 4th character after `'f'` → `column = 4`.
       - Compute the value: `16 × 7 + 4 = 116`.

### 3. **Convert the Value Back to Characters**
   - The number (`116`) corresponds to an ASCII value (characters):
     - ASCII `116` → `'t'`.
   - Map the decoded characters (`mj → t`).

### 4. **Repeat for All Pairs**
   - Continue this for all pairs in the ciphertext:
     ```plaintext
     mj | mk | mh | mj ...
     ```
     Decoded plaintext: `"lkmpifififififff"`.

### 5. **Resolve Layered Dependencies**
   - Since ciphertext blocks depend on previous plaintext blocks, decoding must happen sequentially:
     - Decode the first block → use it to help decode the second block → continue.

---

### **Key Takeaways**
- **Encryption**:
  - Text is converted into pairs, mapped into numbers (using F₁₂₈), and re-encoded as new pairs.
  - Dependencies between blocks make it more secure.
  
- **Decryption**:
  - Reverse the mapping to recover numbers, then convert them back to characters.
  - The sequential dependency requires decoding each block in order.


