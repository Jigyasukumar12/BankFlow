# 🏦 BankFlow

A backend banking ledger service built for managing banking transactions and correctness — double-entry bookkeeping, ACID-safe transfers, and JWT-based security, powered by Node.js, Express, and MongoDB.

---

## 🔄 System Flow

### 1. Authentication

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Client  │───▶│  Express │───▶│   Auth   │───▶│ MongoDB  │
│  Login   │    │  Server  │    │Controller│    │  Lookup  │
└──────────┘    └──────────┘    └────┬─────┘    └──────────┘
                                     │
                                bcrypt.compare()
                                     │
                                     ▼
                          JWT issued (cookie + response body)
```

1. Client sends `email` + `password`.
2. Express server parses the JSON body.
3. Auth Controller looks up user in MongoDB (with password field selected).
4. Server locates the user and compares the password using `bcrypt`.
5. On success, a JWT is signed and returned both as a cookie and in the response body.

### 2. Transfer Flow (10-Step ACID Transfer)

```
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│ Validate │──▶│Idempotent│──▶│  Check   │──▶│  Derive  │──▶│   Open   │
│  Fields  │   │   Key    │   │  Status  │   │  Balance │   │ Session  │
└──────────┘   └──────────┘   └──────────┘   └──────────┘   └────┬─────┘
                                                                  │
                    ┌─────────────────────────────────────────────┘
                    │
                    ▼
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│  Insert  │──▶│  Write   │──▶│  Write   │──▶│  Update  │──▶│  Notify  │
│Txn record│   │  DEBIT   │   │  CREDIT  │   │  status +│   │  by      │
│(PENDING) │   │  entry   │   │  entry   │   │  commit  │   │  email   │
└──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘
```

### 3. Balance Derivation

```
Balance = Σ CREDIT − Σ DEBIT
```

---

## ⚡ Key Features

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
| 🔄 Account Status | Accounts can be `ACTIVE`, `FROZEN`, or `CLOSED` |

---

## 🛠 Technology Stack

| Technology | Purpose |
|---|---|
| Node.js | Runtime environment |
| Express.js | REST API framework |
| MongoDB | Database with native ACID transaction support |
| Mongoose | Schema modeling and validation |
| JWT | Stateless authentication |
| bcrypt | Password hashing |
| Nodemailer (Gmail OAuth2) | Transaction email alerts |

---

## 🚀 Quick Start

```bash
git clone https://github.com/<your-username>/BankFlow.git
cd BankFlow
npm install
```

Create a `.env` file:

```
# .env
PORT=3000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret

# Gmail OAuth2 (for email notifications)
EMAIL_USER=your-email@gmail.com
CLIENT_ID=your_google_client_id
CLIENT_SECRET=your_google_client_secret
REFRESH_TOKEN=your_google_refresh_token
```

Run it:

```bash
npm run dev     # development, hot-reload
npm start       # production
```

---

## 📂 Project Structure

```
BankFlow/
├── server.js
├── src/
│   ├── app.js
│   ├── config/           → database connection
│   ├── controllers/      → auth, account, transaction logic
│   ├── middleware/        → JWT + system-user auth
│   ├── models/            → User, Account, Transaction, Ledger, Blacklist
│   ├── routes/            → REST endpoints
│   └── services/          → email notifications
```

---

## 🔒 Security Architecture

- **Password hashing** — bcrypt with salted hashes, password field excluded from default queries
- **JWT sessions** — signed tokens delivered via cookie and response body, fixed expiry
- **Token blacklisting** — invalidated tokens are stored and auto-expire via a TTL index
- **Role-based access** — separate middleware for regular users vs. system-level operations
- **Immutable ledger** — ledger entries can never be updated or deleted once written

---

## 👤 Author

**Jigyasu Kumar**
