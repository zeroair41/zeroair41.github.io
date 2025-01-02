
RSA (Rivest-Shamir-Adleman) is a public-key cryptographic system based on the mathematics of modular arithmetic and prime factorization. It relies on the difficulty of factoring large numbers.

#  **Encryption and Decryption**:

- In RSA, encryption is `c`= `m ^ e mod n`, and decryption is `m`= `c ^ d mod n`.
- The key relationship is `e * d` ≡ `1 mod  ϕ(n)`, where `ϕ(n)`  is  the Euler totient function of `n`.
# **Euler’s Totient Function ϕ(n)**:

- Counts integers less than `n` that are coprime with `n`.
- For two primes `p` and `q`: `ϕ(n)` = `(p−1)(q−1)`.
# **Role of Modular Inverse**:

- d is the modular inverse of e mod  ϕ(n), ensuring that: 
  `e * d` = `1 + k * ϕ(n)`
- Here, `k` is an integer that accounts for the full multiples of `ϕ(n)`.
# **Proof of Decryption**:

- Decryption computes `c ^ d `=` m ^ (e * d ) mod n`. Substituting` e * d`= `1+k⋅ϕ(n)`: 
  - `c ^ d`= `m ^ (e * d) mod n` = `m ^ (1 + k * ϕ(n)) mod n`.
  - This splits into: `m ^ (1 + k * ϕ(n)) mod n`  = `m * ((m ^ ϕ(n)) ^ k) mod n`.
- By **Euler's Theorem**, `m ^ ϕ(n)` ≡ `1 mod n` (if `m` is coprime to `n`), so:
  - `c ^ d` = `m ⋅ ((m ^ ϕ(n)) ^ k ) mod n`
  - `c ^ d` ≡ `m ⋅ 1 ^ k mod n` 
  - `c ^ d` ≡ `m mod n`
# **Understanding k**:

- `k` represents the number of times `ϕ(n)` is "wrapped around" in` e * d − 1` due to modular arithmetic.
- It does not affect the decryption process since `m ^ ϕ(n)` ≡ `1 mod  n`.
# **Key Insights**:

- RSA decryption works because `e * d` ≡ `1 mod  ϕ(n)`is structured to reverse `m ^ e mod n`. 
- The integer `k` ensures the modular arithmetic equation is balanced but is effectively neutralized in decryption.