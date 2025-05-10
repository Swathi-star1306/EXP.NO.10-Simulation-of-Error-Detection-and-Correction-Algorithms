# EXP.NO.10-Simulation-of-Error-Detection-and-Correction-Algorithms
10.Simulation of Error Detecion and Correction Algorithms

# AIM

To implement a Linear Block Code (LBC) encoder and syndrome-based error detection and correction system using a generator matrix [G = I | P] and parity-check matrix [H = Pᵗ | I] in Python, and to analyze Hamming weight, minimum distance, and error correction capability.

# SOFTWARE REQUIRED

Python 3.x

NumPy library (for matrix operations)

# ALGORITHMS
Step 1: Input Parameters
    
    Input the number of parity bits r and message bits k.
    
    Input the parity matrix P of size k × r.

Step 2: Generator Matrix Construction
    
    Form the identity matrix I of size k × k.
    
    Construct the Generator Matrix G as:
              
               G = [ I | P ]

Step 3: Generate All Possible Message Vectors
    
    Generate all 2^k binary combinations of message bits (e.g., for k = 4, combinations from 0000 to 1111).

Step 4: Encode Message
    
    Multiply each message vector m with the generator matrix G:
              
               c = m × G mod 2

Step 5: Hamming Weight and Minimum Distance
    
    For each codeword, calculate its Hamming weight (number of 1's in the codeword).
    
    Identify the minimum Hamming distance d_min by taking the smallest non-zero Hamming weight.

Step 6: Parity-Check Matrix and Syndrome Calculation
  
    Construct the Parity Check Matrix H as:
                 
                 H = [ Pᵗ | I ]
   
    where Pᵗ is the transpose of matrix P, and I is the identity matrix of size r × r.
    
    For a received vector r, compute the syndrome s:
    
                 s = r × Hᵗ mod 2

Step 7: Error Detection and Correction
    
    Compare the syndrome s with each column of Hᵗ.
    
    If s matches the i-th column of Hᵗ, it indicates a single-bit error at position i.
    
    Flip the erroneous bit in the received codeword to correct it.


# PROGRAM
``` python
import numpy as np

pb = []           # Parity matrix rows
Ik = []           # Identity matrix
p = []
m = []
h_dis = []
r_code = []
err = []

# Input for matrix dimensions
col = int(input("Enter the number of parity bits: "))
row = int(input("Enter the number of message bits: "))

# Input the parity matrix (P)
print("\nEnter the parity matrix rows:")
for i in range(row):
    p = list(map(int, input(f"Row {i+1}: ").split()))
    if len(p) != col:
        raise ValueError(f"Each row must have {col} elements.")
    pb.append(p)

# Convert to numpy array
p_mat = np.array(pb, dtype=int)
Ik = np.eye(row, dtype=int)

# Generator Matrix G = [I | P]
g_mat = np.hstack((Ik, p_mat))

# Codeword length n and message bits k
k = g_mat.shape[0]
n = g_mat.shape[1]

# Generate all possible message vectors (2^k)
all_messages = np.array([[1 if (i >> (k - j - 1)) & 1 else 0 for j in range(k)] for i in range(2 ** k)])

# Generate codewords: c = m * G mod 2
codewords = np.mod(np.dot(all_messages, g_mat), 2)

# Hamming weights
hamming_weights = [np.sum(row) for row in codewords]
d_min = np.min(hamming_weights[1:])

# Parity-check matrix H = [P^T | I]
p_t = p_mat.T
h_check = np.hstack((p_t, np.eye(col, dtype=int)))
ht = h_check.T  # Transpose of H

# Output Generator Matrix
print("\n**********")
print("Generator Matrix [G = I | P]:")
for row in g_mat:
    print("".join(map(str, row)))

# Output Codewords with Proper Formatting
print("\n**********")
print("{:<15} {:<15} {:<15}".format("Message Bits", "Codeword", "Hamming Weight"))
for i in range(len(all_messages)):
    msg_str = "".join(map(str, all_messages[i]))
    code_str = "".join(map(str, codewords[i]))
    weight = hamming_weights[i]
    print("{:<15} {:<15} {:<15}".format(msg_str, code_str, weight))

# Minimum Hamming distance
print("\n**********")
print(f"Minimum Hamming Distance: {d_min}")

# Output Parity Check Matrix
print("\n**********")
print("Parity Check Matrix [H = P^T | I]:")
for row in h_check:
    print("".join(map(str, row)))

# Output Transpose of Parity Check Matrix
print("\n**********")
print("Transpose of Parity Check Matrix (H^T):")
for row in ht:
    print("".join(map(str, row)))

# Receive codeword
rc = list(map(int, input("\nEnter the received codeword: ").split()))
if len(rc) != n:
    raise ValueError("Received codeword length must match codeword length n.")
r_c = np.array([rc])

# Syndrome calculation: s = r * H^T mod 2
syndrome = np.mod(np.dot(r_c, ht), 2).flatten()

# Find error position
err = np.zeros(n, dtype=int)
for i in range(n):
    if np.array_equal(syndrome, ht[i]):
        err[i] = 1
        break

print("\n**********")
print("Syndrome:", "".join(map(str, syndrome)))
print("Error vector:", "".join(map(str, err)))

# Correct the error
corrected = (r_c.flatten() + err) % 2
print("Corrected Codeword:", "".join(map(str, corrected)))

# Optional: Syndrome Table (first few entries)
print("\n**********")
print("Syndrome Matrix:")
for i in range(n):
    s = ht[i]
    ev = np.eye(n, dtype=int)[i]
    print(f"{' '.join(map(str, s))}  {' '.join(map(str, ev))}")

print("**********")
```

# OUTPUT
Enter the number of parity bits: 3
Enter the number of message bits: 4

Enter the parity matrix rows:
Row 1: 1 0 1
Row 2: 1 1 0
Row 3: 0 1 1
Row 4: 1 1 1

**********
Generator Matrix [G = I | P]:
1000101
0100110
0010011
0001111

**********
Message Bits    Codeword        Hamming Weight 
0000            0000000         0              
0001            0001111         4              
0010            0010011         3              
0011            0011100         3              
0100            0100110         3              
0101            0101001         3              
0110            0110101         4              
0111            0111010         4              
1000            1000101         3              
1001            1001010         3              
1010            1010110         4              
1011            1011001         4              
1100            1100011         4              
1101            1101100         4              
1110            1110000         3              
1111            1111111         7              

**********
Minimum Hamming Distance: 3

**********
Parity Check Matrix [H = P^T | I]:
1101100
0111010
1011001

**********
Transpose of Parity Check Matrix (H^T):
101
110
011
111
100
010
001

Enter the received codeword: 1 0 1 0 1 0 1

**********
Syndrome: 011
Error vector: 0010000
Corrected Codeword: 1000101

**********
Syndrome Matrix:
1 0 1  1 0 0 0 0 0 0
1 1 0  0 1 0 0 0 0 0
0 1 1  0 0 1 0 0 0 0
1 1 1  0 0 0 1 0 0 0
1 0 0  0 0 0 0 1 0 0
0 1 0  0 0 0 0 0 1 0
0 0 1  0 0 0 0 0 0 1
**********



**OUTPUT IMAGE**


![image](https://github.com/user-attachments/assets/3901952c-4c6c-4f1c-920a-e1f173d5c571)

![image](https://github.com/user-attachments/assets/325d96cc-27d2-483c-9405-38d2a7f9c5fb)



**THEORETICAL VERIFICATION**


![WhatsApp Image 2025-05-10 at 10 49 27_033e55af](https://github.com/user-attachments/assets/78a221ad-2cae-4e71-ae11-9f35fe6be9d5)

![WhatsApp Image 2025-05-10 at 10 49 27_444c740d](https://github.com/user-attachments/assets/17374b68-e3ae-4f17-833b-ad6de06086d5)

# RESULT / CONCLUSIONS

The code successfully generates all possible codewords using the Generator Matrix and calculates the minimum Hamming distance as 3, enabling single-bit error correction.
Syndrome decoding identifies and corrects errors in received codewords using the Parity Check Matrix.









