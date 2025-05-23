# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC
## NAME:LOSHINI G
## REGISTER NO:212223220051
## DEPARTMENT:IT
## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```
#include <stdio.h>

typedef struct {
    long long int x, y;
    int is_infinity; // 1 if the point is at infinity
} Point;

long long int modInverse(long long int a, long long int m) {
    long long int m0 = m, t, q;
    long long int x0 = 0, x1 = 1;
    if (m == 1) return 0;
    while (a > 1) {
        q = a / m;
        t = m;
        m = a % m;
        a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
    }
    if (x1 < 0) x1 += m0;
    return x1;
}

Point pointAddition(Point P, Point Q, long long int a, long long int p) {
    Point R;

    if (P.is_infinity) return Q;
    if (Q.is_infinity) return P;

    if (P.x == Q.x && (P.y + Q.y) % p == 0) {
        R.is_infinity = 1;
        return R;
    }

    long long int lambda;
    if (P.x == Q.x && P.y == Q.y) {
        long long int num = (3 * P.x * P.x + a) % p;
        long long int den = modInverse(2 * P.y, p);
        lambda = (num * den) % p;
    } else {
        long long int num = (Q.y - P.y + p) % p;
        long long int den = modInverse((Q.x - P.x + p) % p, p);
        lambda = (num * den) % p;
    }

    R.x = (lambda * lambda - P.x - Q.x + p) % p;
    R.y = (lambda * (P.x - R.x + p) - P.y + p) % p;
    R.x = (R.x + p) % p;
    R.y = (R.y + p) % p;
    R.is_infinity = 0;

    return R;
}

Point scalarMultiplication(Point P, long long int k, long long int a, long long int p) {
    Point result = {0, 0, 1}; // Point at infinity
    while (k > 0) {
        if (k % 2 == 1)
            result = pointAddition(result, P, a, p);
        P = pointAddition(P, P, a, p);
        k = k / 2;
    }
    return result;
}

int isPointOnCurve(Point P, long long int a, long long int b, long long int p) {
    long long int left = (P.y * P.y) % p;
    long long int right = (P.x * P.x * P.x + a * P.x + b) % p;
    return left == right;
}

int main() {
    long long int p, a, b, privateA, privateB;
    Point G, publicA, publicB, sharedA, sharedB;

    printf("Enter the prime number (p): ");
    scanf("%lld", &p);

    printf("Enter curve parameters a and b for y^2 = x^3 + ax + b: ");
    scanf("%lld %lld", &a, &b);

    if ((4 * a * a * a + 27 * b * b) % p == 0) {
        printf("Invalid curve. Discriminant is zero.\n");
        return 1;
    }

    printf("Enter base point G (x y): ");
    scanf("%lld %lld", &G.x, &G.y);
    G.is_infinity = 0;

    if (!isPointOnCurve(G, a, b, p)) {
        printf("Base point G is not on the curve.\n");
        return 1;
    }

    printf("Enter Alice's private key: ");
    scanf("%lld", &privateA);
    printf("Enter Bob's private key: ");
    scanf("%lld", &privateB);

    publicA = scalarMultiplication(G, privateA, a, p);
    publicB = scalarMultiplication(G, privateB, a, p);

    printf("Alice's public key: (%lld, %lld)\n", publicA.x, publicA.y);
    printf("Bob's public key: (%lld, %lld)\n", publicB.x, publicB.y);

    sharedA = scalarMultiplication(publicB, privateA, a, p);
    sharedB = scalarMultiplication(publicA, privateB, a, p);

    printf("Shared key by Alice: (%lld, %lld)\n", sharedA.x, sharedA.y);
    printf("Shared key by Bob: (%lld, %lld)\n", sharedB.x, sharedB.y);

    if (sharedA.x == sharedB.x && sharedA.y == sharedB.y) {
        printf("Key exchange successful. Both shared secrets match.\n");
    } else {
        printf("Key exchange failed. Shared secrets do not match.\n");
    }

    return 0;
}
```


## Output:
![CRYPTO EX 11](https://github.com/user-attachments/assets/fe63fa9b-15f3-48fa-a494-f5cb0f346192)


## Result:
The program is executed successfully

