# EX-NO-10-Diffie-Hellman-Key-Exchange-Algorithm

## AIM:
To Implement Diffie Hellman Key Exchange Algorithm 

## Algorithm:

1. Diffie-Hellman Key Exchange is used for securely sharing a secret key between two parties over an insecure channel.

2. Initialization: Agree on a large prime number \( p \) and a primitive root \( g \) modulo \( p \) (both are public values).

3. Key Exchange Process: 
   - Each party selects a private key and calculates their public key using the formula \( g^{\text{private key}} \mod p \).
   - Each party then shares their public key with the other.

4. Secret Key Computation: 
   - Each party computes the shared secret key using the received public key and their own private key.

5. Security: The difficulty of computing discrete logarithms ensures that the shared key remains secure even if public values are intercepted.

## Program:
```
#include <stdio.h>

typedef struct {
    int x;
    int y;
    int is_inf;   // 1 if point at infinity
} Point;

int p = 17;  // prime field
int a = 2;   // curve parameter

// Modular inverse (very small & simple)
int mod_inverse(int k) {
    k = k % p;
    for (int i = 1; i < p; i++)
        if ((k * i) % p == 1)
            return i;
    return -1;
}

// Point addition
Point add(Point P, Point Q) {
    Point R;

    if (P.is_inf) return Q;
    if (Q.is_inf) return P;

    if (P.x == Q.x && (P.y + Q.y) % p == 0) {
        R.is_inf = 1;
        return R;  // Point at infinity
    }

    int m;
    if (P.x == Q.x && P.y == Q.y) {
        // Point doubling
        m = (3 * P.x * P.x + a) * mod_inverse(2 * P.y) % p;
    } else {
        // Point addition
        m = (Q.y - P.y) * mod_inverse(Q.x - P.x) % p;
    }

    if (m < 0) m += p;

    R.x = (m * m - P.x - Q.x) % p;
    if (R.x < 0) R.x += p;

    R.y = (m * (P.x - R.x) - P.y) % p;
    if (R.y < 0) R.y += p;

    R.is_inf = 0;
    return R;
}

// Scalar multiplication (d × G)
Point multiply(Point P, int k) {
    Point R = {0, 0, 1};  // Start with infinity

    for (int i = 0; i < k; i++) {
        R = add(R, P);
    }
    return R;
}

int main() {
    Point G = {5, 1, 0};

    // ----- KEY GENERATION -----
    int private_key = 7;  // Secret key (random)
    Point public_key = multiply(G, private_key);

    printf("Private Key: %d\n", private_key);
    printf("Public Key: (%d, %d)\n", public_key.x, public_key.y);

    // ----- MESSAGE ENCRYPTION -----
    // Toy "message point"
    Point M = {6, 3, 0};  // Pretend this is your message encoded

    int k = 3;  // random session key
    Point C1 = multiply(G, k);
    Point kQ = multiply(public_key, k);
    Point C2 = add(M, kQ);

    printf("\nCiphertext C1: (%d, %d)\n", C1.x, C1.y);
    printf("Ciphertext C2: (%d, %d)\n", C2.x, C2.y);

    // ----- MESSAGE DECRYPTION -----
    Point dC1 = multiply(C1, private_key);

    // Compute M = C2 - dC1  → add with inverse y
    Point temp = { dC1.x, (p - dC1.y) % p, 0 };
    Point decrypted = add(C2, temp);

    printf("\nDecrypted Message: (%d, %d)\n", decrypted.x, decrypted.y);

    return 0;
}
```
## Output:
<img width="806" height="381" alt="image" src="https://github.com/user-attachments/assets/f735ad1e-78c2-4ec2-b312-1f5ec89d6426" />

## Result:
The program is executed successfully.
