---
layout: post
title:  "SEV Memory Encryption"
author: mengyuan
categories: [ Jekyll, tutorial ]
image: assets/images/xex.png
---
# XEX Encryption

SEV hardware encrypts the VM's memory using 128-bit AES symmetric encryption. The AES engine integrated into the AMD System-on-Chip (SOC) automatically encrypts the data when it is written to the memory and automatically decrypts the data when it is read from memo

![encryption_1]({{ site.baseurl }}/assets/images/encryption_1.png)

For SEV, the AES encryption uses the XOR-and-Encrypt encryption mode[1](shown in figure a), which is later changed to an XEX mode encryption (shown in figure b).

![encryption_2]({{ site.baseurl }}/assets/images/encryption_2.png)

Thus, each aligned 16-byte memory block is encrypted independently. SEV utilizes a physical address-based tweak function T() to prevent the attacker from directly inferring plaintext by comparing 16-byte ciphertext[2]. It adopts a basic Xor-and-Encrypt (XE) mode on the first generation of EPYC processors (e.g., EPYC 7251). The ciphertext *c* is calculated by XORing the plaintext *m* with the tweak function for system physical address $P_m$ using 

$$c = ENC(m \oplus T(P_{m}))$$

where the encryption key is called VM encryption key ($K_{vek}$). This basic XE encryption mode can be easily reverse-engineered by the adversary as the tweak function vectors $t_i$s are fixed. AMD then replaces the XE mode encryption with the XOR-Encrypt-XOR (XEX) mode in EPYC 7401P processors where the ciphertext is calculated by 

$$c = ENC(m \oplus T(P_{m}))\oplus T(P_{m})$$

The tweak function vectors  $t_i$s are proved to have only 32-bit entropy by Wilke et al.[3] at first, which allows an adversary to reverse engineer the tweak function vectors. AMD adopted a 128-bit entropy tweak function vectors in their Zen 2 architecture EPYC processors from July 2019[4] and thus fixed all existing vulnerabilities in SEV AES encryption. However, the same plaintext always has the same ciphertext in system physical address $P_m$ during the lifetime of a guest VM.

## Reference

[1]Du, Zhao-Hui, Zhiwei Ying, Zhenke Ma, Yufei Mai, Phoebe Wang, Jesse Liu, and Jesse Fang. "Secure encrypted virtualization is unsecure." arXiv preprint arXiv:1712.05090 (2017). 

[2]Kaplan, David, Jeremy Powell, and Tom Woller. "AMD memory encryption." White paper (2016).

[3] Wilke, Luca, Jan Wichelmann, Mathias Morbitzer, and Thomas Eisenbarth. "SEVurity: No Security Without Integrity: Breaking Integrity-Free Memory Encryption with Minimal Assumptions." In 2020 IEEE Symposium on Security and Privacy (SP), pp. 1483-1496. IEEE, 2020.

[4]Suggs, David, Mahesh Subramony, and Dan Bouvier. "The AMD “Zen 2” Processor." IEEE Micro 40, no. 2 (2020): 45-52.