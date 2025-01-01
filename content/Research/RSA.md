# Mastering RSA Digital Signatures: Encrypting & Authenticating Messages with Python

# Introduction 

Imagine needing to send a secret message to a friend while also proving that it was really you who sent it. RSA encryption and digital signatures are two crucial components that help ensure both security and authenticity. Today, we’ll dive into how RSA signatures work and demonstrate how to implement them in Python.

We’ll cover:

1. Generating public and private keys
2. Encrypting a message for secure communication
3. Signing a message for authenticity
4. Verifying the signature to confirm its source

Let’s jump in!

# A Brief Overview of RSA:

RSA is an asymmetric cryptographic algorithm used for both encryption and signing. It relies on a public-private key pair:

- The **public key** is used for encrypting data and verifying signatures. It can be shared openly.
- The **private key** is used for decrypting data and creating signatures. It must be kept secure.

The process of **signing** allows you to authenticate the origin of a message. Essentially, by signing a message, you’re using your private key to generate a unique signature, which anyone with your public key can verify.

# Step 1: Generating RSA Keys:

To follow along, you need RSA keys. For this example, let’s generate our own keys.

The process of key generation involves:

1. Selecting two large prime numbers, p and q.
2. Calculating N=p×q.
3. Calculating ϕ(N)=(p−1)×(q−1)
4. Selecting a public exponent e, which is typically 65537.
5. Determining the private key d, which is the modular inverse of e modulo ϕ(N).

Here’s the code:

```from Crypto.Util import *  
  
# Step 1: Select two large prime numbers, p and q  
p = getPrime(512)  # 512-bit prime for example purposes  
q = getPrime(512)  
  
# Step 2: Calculate N = p * q  
N = p * q  
  
# Step 3: Calculate ϕ(N) = (p - 1) * (q - 1)  
phi_N = (p - 1) * (q - 1)  
  
# Step 4: Select a public exponent e  
e = 65537  # Commonly used value for e  
  
# Step 5: Calculate the private key d, which is the modular inverse of e modulo ϕ(N)  
d = pow(e, -1, phi_N)  
  
# Printing the keys  
print("Public Key (N, e):")  
print(f"N = {N}")  
print(f"e = {e}")  
  
print("\nPrivate Key (N, d):")  
print(f"N = {N}")  
print(f"d = {d}")
```

This setup ensures that you have both an **encryption key (public key)** and a **decryption key (private key)**.

Let’s now put our keys to use!

# Step 2: Signing and Encrypting the Message:

Here’s the scenario:

- You, the sender, will **sign** the message using your **private key**.
- You will then **encrypt** the signed message using your friend’s **public key** so only they can read it.

Here’s the code:

```from Crypto.Util.number import *  # Importing utility functions for number manipulation  
from hashlib import sha256  # Importing the SHA-256 hashing algorithm  
  
# Own RSA keys (private and public)  
own_decryption_key = 557039820478963630987824353291578599136059185493036744361619995981604349033509275187006036413348457353229065590145649264317981320495265828225691250227436627950380140312691345995567216885209096438411775703891209812905070006859405226963760362777288605320416723513198803620224752470085103470630892040803115073  # Private key  
own_public_key = 60341683825999734684378586184578820911702331966375453083020644093630420202659665070960024146151434462080286399308058538571252135208757415842028309861414088754201429208593194406919737052886050899503374401255680985303985305673539849312801504843108333165972740331833871923484768429390468329882756377536602788349  # Public key modulus  
own_public_key_exponent = 65537  # Public key exponent, commonly chosen as 65537 for efficiency  
  
# Friend's public RSA key  
friend_public_key_exponent = 65537  # Friend's public key exponent  
friend_public_key = 124635880425463236816405829018856743683640397655827026728571600981597987344870783920887353388732835598504050402809812368672914825753659518155588470385920050713463078544219562695491794678584987346544532871623557706254621928742810050937220512991910307218321820634633967793468886068548140689985949102802348388297  # Friend's public key modulus  
  
# Message that needs to be signed and encrypted  
message = b'Hello are you ready for plan?'  # The message in bytes format  
message_hash = sha256(message).digest()  # Creating a hash of the message using SHA-256 for signing  
  
# Signing the message with the private key  
# Signature is created by encrypting the hash of the message with your private key  
signature = pow(bytes_to_long(message_hash), own_decryption_key, own_public_key)  
  
# Encrypting the message using friend's public key  
# Message is encrypted using the friend's public key, ensuring only they can decrypt it  
cipher = pow(bytes_to_long(message), friend_public_key_exponent, friend_public_key)  
  
# Print the results: the encrypted message and the signature  
print(f'Message (Encrypted): {cipher}\nSignature: {signature}')
```

## Explanation:

1. **Hashing the Message**: The message is hashed using SHA-256, creating a fixed-length representation.
2. **Creating the Signature**: The message hash is then signed using the sender’s **private key**.
3. **Encrypting the Message**: To ensure that only the intended recipient can read it, the message is encrypted with the recipient’s **public key**.

# Step 3: Receiving and Verifying the Message:

In this step, the recipient needs to do two things:

1. **Decrypt the Encrypted Message**: Use their private key to recover the original plaintext.
2. **Verify the Signature**: Confirm that the message came from the original sender by validating the signature against the message.

Here’s the code:

```from Crypto.Util.number import *  # Importing utility functions for RSA operations  
from hashlib import sha256  # Importing SHA-256 for hashing  
  
# Own RSA keys (private and public)  
own_decryption_key = 31673872598472469127015870154402231808764985015453852482786212587523299498433295790200632782845497610404579999981650441738977927322385816788704487148900591601528863541514670188936578415139141174908460027854808646631844926645660317064936857518667158075218424622227225133356598250749524655098629092161146664573  # Private key for decryption  
own_public_key = 124635880425463236816405829018856743683640397655827026728571600981597987344870783920887353388732835598504050402809812368672914825753659518155588470385920050713463078544219562695491794678584987346544532871623557706254621928742810050937220512991910307218321820634633967793468886068548140689985949102802348388297  # Public key modulus  
own_public_key_exponent = 65537  # Public key exponent, commonly used in RSA  
  
# Friend's public RSA key (used for signature verification)  
friends_public_key_exponent = 65537  # Friend's public key exponent  
friends_public_key = 60341683825999734684378586184578820911702331966375453083020644093630420202659665070960024146151434462080286399308058538571252135208757415842028309861414088754201429208593194406919737052886050899503374401255680985303985305673539849312801504843108333165972740331833871923484768429390468329882756377536602788349  # Friend's public key modulus  
  
# Provided cipher and signature to be decrypted and verified  
cipher = 9459108173003176190658917736941301532272586971645583670875775165172391329845601792504307808351643956307431118302360314767699258426677592288858615638593094222949415030849764879772780790105644414585101106677494517560933062296609696906469401549150450745356000816694954663645455400600365767954826214540178419093  # Ciphertext to be decrypted  
signature = 33829722817935910459875952070093095253067934165815148526692616093688549131778290333954623709787041074907543029037800895420867374622699516591439890658384141827859151441207576258836035592798674624497440090100050212454390790278732092215561272123753038068161917779728339107529254612832423370788474792753216079756  # Signature to be verified  
  
# Step 1: Decrypt the message using the private key  
# Decryption involves using the private key to compute the plaintext message from the ciphertext  
message = long_to_bytes(pow(cipher, own_decryption_key, own_public_key))  
  
# Step 2: Hash the decrypted message to verify the signature  
# A hash of the decrypted message is computed for verification purposes  
message_hash = sha256(message).digest()  
  
# Step 3: Verify the signature using the friend's public key  
# The signature is verified by checking if the provided signature matches the expected value  
# The signature is decrypted using the friend's public key to see if it matches the hashed value  
if bytes_to_long(message_hash) == pow(signature, friends_public_key_exponent, friends_public_key):  
    print(f'Signature Matched\nMessage: {message.decode()}')  # Signature verification succeeded  
else:  
    print(f'Signature Verification Failed')  # Signature verification failed
```

## Explanation

1. **Decrypting the Message**: The recipient decrypts the message using their **private key**.
2. **Verifying the Signature**: The decrypted message is hashed again and compared with the hash obtained by decrypting the provided signature using the sender’s **public key**.

# Conclusion

By signing a message with your private key, you ensure its authenticity, and by encrypting it with the recipient’s public key, you guarantee confidentiality. RSA provides a secure way to accomplish both goals.

The code above demonstrates how to sign, encrypt, verify, and decrypt using RSA keys — all in Python! Whether you’re sharing plans with friends or exploring cryptography, RSA is a powerful tool for secure communication.

Feel free to experiment with the code to deepen your understanding. And if you found this guide helpful, share it with others who might be interested in cryptographic magic!

Happy hacking! 🚀