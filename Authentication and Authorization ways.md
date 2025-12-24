# Authentication & Authorization — Simple Mental Models

This document summarizes common authentication and authorization mechanisms using **problem → solution** mental models.  
The goal is clarity, not protocol-level detail.

#TLDR (For quick revision);
## Final One-Line Anchors

- **SSH** → Prove possession  
- **JWT** → Permissions inside the token  
- **PAT** → Permissions in the server’s register  
- **SSO** → Reuse verified identity  
- **OAuth** → Delegate access safely  
- **SAML** → Company owns identity  

## When to use

Different problems need different tools:

- Users need proof of possession → SSH  
- Apps need permission control → JWT / PAT  
- Users need convenience → SSO  
- Apps need delegated access → OAuth  
- Companies need authority → SAML  

---



## 1. SSH (Secure Shell)

**Problem**  
A server needs to verify that the connecting machine is trusted, by asking the machine to solve a secret puzzle.

**Solution**  
The server sends a challenge.  
The client proves it owns a private key by answering correctly.  
The private key never leaves the machine.

**Mental model**  
> “Prove you own the key without showing the key.”

**Key traits**
- Verifies machine possession
- Secrets never travel over the network thus protected against leaks.
- Ideal for human interaction, repeated access from the same machine (servers, git push)
- Should not be used by automated pipelines or apps as they can have security bugs(id logging, delete logic) which can compromise the entire repo.

---

## 2. JWT vs PAT (Permission Verification)

Both answer: **“What am I(user/app) allowed to do?”**

### A. JWT (JSON Web Token)

**Problem**  
The server wants to avoid checking a database on every request.

**Solution**  
Permissions are written directly inside the token.  
The server verifies the token and trusts its contents.

**Mental model**  
> “My powers are written on my ID card itself.”

**Implications**
- Very fast as the powers are written on card.
- Permission cannot be revoked as it cannot be removed from the card.
- Good for web sessions and scale as it will be fast.
- Even if leaked, the damage can be minimized.

---

### B. PAT (Personal Access Token)

**Problem**  
The server wants to have the power to control your permissions.

**Solution**  
The token is just an ID.  
The server looks it up in its own register to decide permissions.

**Mental model**  
> “My ID card has only a number. The authority owns the register of permissions for my id.”

**Implications**
- Slower (DB lookup)
- Strong central control
- Ideal for use by CI, automation, scripts which should be given limited powers.
- Even if leaked, the damage can be minimized.

---

## 3. SSO (Single Sign-On)

**Problem**  
Users don’t want to log in separately to many applications.

**Solution**  
Once identity is verified by a trusted system(e.g. google), that proof is reused.

**Mental model**  
> “I already proved who I am. Don’t ask again.”

**Important**
- SSO is an **experience**, not a protocol
- Implemented using cookies, OAuth, SAML, etc.

---

## 4. OAuth

**Problem**  
An application needs limited access to a user’s data without knowing the user’s password.

**Solution**  
A trusted authority(e.g. google) issues a token that allows only specific actions.

**Mental model**  
> “You may access some of my data, but not everything.”

**Key traits**
- User-controlled permission
- No password sharing
- Common in “Login with Google/GitHub”

---

## 5. SAML

**Problem**  
A company wants to control employee identity centrally instead of letting every app manage logins.

**Solution**  
Applications trust the company to confirm whether a user is valid.

**Mental model**  
> “Ask my employer if I’m valid. Don’t ask me.”

**Key traits**
- Company-controlled authentication
- Apps do not store passwords
- Enables enterprise-wide SSO



