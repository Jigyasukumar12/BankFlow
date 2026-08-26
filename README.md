# 🏦 BankFlow

A backend banking ledger service that moves money the way real banks do — through double-entry bookkeeping, atomic transfers, and a ledger that never lies. Built with Node.js, Express, and MongoDB.

---

## 💡 Why BankFlow?

Most beginner banking projects just update a `balance` field and call it done — which breaks the moment two transfers happen at the same time. BankFlow avoids that trap entirely: **balances are never stored**, they're always calculated fresh from an immutable ledger, so the numbers can never drift out of sync.

---

## ⚙️ Core Highlights

| Feature | What it means |
|---|---|
| 🔐 JWT Authentication | Secure login with hashed passwords and token-based sessions |
| 🛡️ Token Blacklisting | Logged-out tokens are instantly invalidated, not just ignored |
| 📒 Double-Entry Ledger | Every rupee moved creates a DEBIT and a CREDIT entry — nothing is ever overwritten |
| ⚛️ ACID Transfers | MongoDB sessions guarantee a transfer either fully completes or fully rolls back |
| 🔑 Idempotency Keys | Retried requests never cause duplicate transfers |
| 💰 Derived Balances | Account balance is computed live via aggregation, not stored as a number |
| 📧 Email Alerts | Users get notified automatically after a successful transaction |
| 🏦 Multi-Account Support | One user, multiple accounts, independent balances |

---

## 🛠 Tech Stack

**Node.js · Express.js · MongoDB · Mongoose · JWT · bcrypt · Nodemailer (Gmail OAuth2)**

---

## 🔄 How a Transfer Actually Happens

1. Request comes in with sender, receiver, amount, and a unique idempotency key
2. Server checks it hasn't already been processed
3. Sender's real-time balance is derived from the ledger (not read from a stored field)
4. A MongoDB session opens — a debit entry and a credit entry are written together
5. If anything fails midway, the whole transaction is rolled back — no half-finished transfers
6. On success, the session commits and an email notification goes out

---

## 📂 Project Structure

```
BankFlow/
├── server.js
├── src/
│   ├── app.js
│   ├── config/          → database connection
│   ├── controllers/      → auth, account, transaction logic
│   ├── middleware/       → JWT + system-user auth
│   ├── models/           → User, Account, Transaction, Ledger, Blacklist
│   ├── routes/           → REST endpoints
│   └── services/         → email notifications
```

---

## 🚀 Getting Started

```bash
git clone https://github.com/Jigyasukumar12/BankFlow.git
cd BankFlow
npm install
npm run dev
```


---

## 👤 Author

**Jigyasu Kumar**

---

> Built to understand how real financial systems stay consistent — not just how to move numbers around.
