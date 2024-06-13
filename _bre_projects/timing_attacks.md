---
title: "Mitigating Timing Side Channel Attacks with Constant-Time Algorithms"
author_profile: true
key: 1
toc: true
excerpt: "Python, Data Analysis, Privacy"
header:
  teaser: /bre_images/ti_at-teaser.png
classes: wide
---

### Introduction
Timing side channel attacks pose a significant threat to the security of cryptographic systems, allowing attackers to exploit variations in execution time to extract sensitive information. This report investigates the effectiveness of constant-time algorithms in mitigating these attacks, focusing on modular exponentiation, a crucial operation in public key cryptography.

### Background
Public key cryptography relies on modular exponentiation for key generation, encryption, and decryption. Traditional implementations are vulnerable to timing attacks, where differences in execution time reveal information about secret parameters, such as the private key.

### Naive Implementation
I began by implementing a basic modular exponentiation algorithm without any countermeasures. This naive approach iteratively multiplies the base by itself exponent times and takes the result modulo the modulus. Initial testing revealed errors such as dividing by zero, which I resolved by setting the modulo to 1 if it equaled 0. Despite these fixes, the implementation remained susceptible to timing attacks due to runtime variations with the exponent's value.

### Testing the Naive Algorithm
I tested the naive algorithm's runtime using a function that executes the algorithm on various test cases and measures execution time. Random numbers of the specified bit length were generated for each test. Each test involved 50 computations, and the results were analyzed to find the mean, variance, and standard deviation.

Initial tests with small numbers showed similar results, but issues arose with larger, typical 1024-bit numbers used in public key cryptography. The naive implementation exhibited unusable runtimes and drastic variations within each dataset.

#### Results

![Naive Algorithm](/bre_images/ti_at_naive.png)

| Num of bits | Mean      | Variance   | St Dev  |
|-------------|-----------|------------|---------|
| 10          | 0.000015  | 0.0000000001 | 0.00001  |
| 15          | 0.0026    | 0.00000386   | 0.0019   |
| 20          | 0.025     | 0.00019      | 0.013    |
| 25          | 0.796     | 0.29         | 0.539    |
| 27          | 3.305     | 3.496        | 1.86     |
| 30          | 31.78     | 236.41       | 15.37    |

### Constant-Time Algorithm
To mitigate timing attacks, I implemented a constant-time method for modular exponentiation. This approach iterates through each bit of the exponent, adjusting the result accordingly, ensuring consistent execution time regardless of input values.

### Testing the Constant-Time Algorithm
I conducted the same tests on the constant-time algorithm as the naive implementation. The results showed much faster and more consistent performance for 1024-bit functions, making it harder for attackers to exploit timing attacks.

#### Results

![Constant Algorithm](/bre_images/ti_at_const.png)

| Num of Bits | Mean (secs) | Variance       | St Dev  |
|-------------|--------------|----------------|---------|
| 250         | 0.0002       | 0.00000000028  | 1.699   |
| 512         | 0.0012       | 0.000000026    | 0.000164|
| 1024        | 0.0066       | 0.000000057    | 0.000239|
| 2048        | 0.0438       | 0.0000013      | 0.00115 |
| 4096        | 0.326        | 0.00014        | 0.011   |
| 8192        | 2.372        | 0.001          | 0.0375  |
| 16384       | 18.675       | 0.115          | 0.34    |

### Masking Algorithm
I also explored a masking algorithm that reduces the exponent into 16-bit chunks using a bitmask and processes each chunk independently. This method minimizes the risk of timing attacks exploiting execution time variations.

### Testing the Masking Algorithm
The masking algorithm underwent the same tests, and results were compared to the constant-time algorithm. While the masking algorithm was slightly faster, it exhibited higher variance, which could potentially lead to vulnerabilities with larger inputs.

#### Results

![Masking Algorithm](/bre_images/ti_at_mask.png)

| Num of Bits | Mean       | Variance       | St Dev  |
|-------------|------------|----------------|---------|
| 250         | 0.0001     | 0.00000000015  | 0.000012|
| 512         | 0.0011     | 0.000000012    | 0.00011 |
| 1024        | 0.00618    | 0.0000001      | 0.000317|
| 2048        | 0.0399     | 0.0000015      | 0.00125 |
| 4096        | 0.295      | 0.000009       | 0.003   |
| 8192        | 2.188      | 0.025          | 0.159   |
| 16384       | 17.173     | 0.127          | 0.356   |

### Conclusion
Constant-time algorithms and masking techniques significantly mitigate timing attacks but other side-channel vulnerabilities, such as fault injection and network-based attacks, remain risks. Addressing these concerns may require additional countermeasures like memory access pattern randomization and hardware-based protections.
