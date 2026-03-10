# HTTPS / TLS Handshake — Simplified Mental Model

## Assumption

Treat the verification process of SSL certificates as a **black box**.

Just know the following rule:

Man-in-the-middle(i.e. hacker between server and client) can intercept or modify the certificate, but cannot produce a valid CA signature, so the browser rejects the connection.

Understanding the internal verification process requires studying:

- Certificate chains
- Digital signatures
- Root and intermediate CAs
- Browser trust stores

For now, treat it as a system that verifies the identity of the server.

---

# What is HTTPS?

HTTPS is simply:

HTTP + TLS Encryption

It ensures:

- Data cannot be read by attackers
- Data cannot be modified in transit
- The browser can verify the server identity

---

# TLS Handshake (Simplified Flow)

When a browser connects to a server, the following happens.

## 1. Browser initiates secure connection

The browser starts a TLS handshake with the server.

At this stage, no HTTP data is sent yet.

---

## 2. Server sends SSL certificate

The server sends its SSL certificate.

A certificate contains:

- domain name
- server public key
- issuer (CA)
- expiry date
- CA signature

---

## 3. Browser verifies the certificate

The browser verifies the certificate to confirm the server's identity.

If verification fails, the browser shows:

Connection is not secure

If verification succeeds, the connection proceeds.

---

## 4. Browser extracts the server public key

From the certificate, the browser extracts the **server public key**.

---

## 5. Browser generates a session key

The browser generates a random session key.

session_key = random()

This key will be used for encrypting all communication.

---

## 6. Browser encrypts the session key

The browser encrypts the session key using the server's public key.

encrypted_session_key = encrypt(session_key, server_public_key)

---

## 7. Browser sends the encrypted session key

The encrypted session key is sent to the server.

Even if someone intercepts it, they cannot read it.

---

## 8. Server decrypts the session key

The server decrypts it using its private key.

session_key = decrypt(encrypted_session_key, server_private_key)

This process where a public key encrypts and a private key decrypts is called **public key encryption**.

---

## 9. Secure communication begins

Now both the browser and server share the same **session key**.

All future communication is encrypted using this session key.

Example:

encrypted_data = encrypt(http_data, session_key)

Receiver decrypts it using the same key:

http_data = decrypt(encrypted_data, session_key)

This process where the **same key is used for encryption and decryption** is called **symmetric encryption**.

---

# Important Takeaway

Public key encryption → secure key exchange  
Symmetric encryption → fast data encryption
