# How to Build a Secure Blockchain Transaction Verification Engine in Node.js

## A step-by-step guide to verifying XRP Ledger payments the right way

> **Estimated reading time:** 9 minutes

---

# The Problem Most Web3 Beginners Don't Notice

Imagine you've just built your first Web3 application.

A customer clicks **Pay with XRP**, their wallet confirms the transaction, and your frontend proudly displays:

> ✅ Payment Successful

The frontend then sends a request to your backend:

```json
{
    "status": "paid",
    "transactionHash": "..."
}
```

Your backend trusts the request and unlocks the user's premium content.

Everything seems perfect...

Until someone opens their browser's Developer Tools, modifies the request, changes `"status"` to `"paid"` without ever sending XRP, and suddenly gains access for free.

This isn't a bug in the XRP Ledger.

It's a security flaw in your application.

One of the biggest misconceptions in Web3 development is believing the frontend should tell the backend whether a payment succeeded.

It shouldn't.

The blockchain is the source of truth—not your frontend.

In this article, we'll build a secure payment verification engine using Node.js and the official XRPL SDK that verifies every payment directly from the XRP Ledger.

---

# Why Backend Verification Matters

A secure blockchain application follows a very different flow.

```
User
 │
 │ Sends XRP
 ▼
XRP Ledger
 │
 │
 ▼
Backend requests transaction
 │
 ├── Was it successful?
 ├── Was it sent to my wallet?
 ├── Is the amount correct?
 ├── Has it already been processed?
 │
 ▼
Grant access
```

Notice something important.

The frontend never tells the backend that payment succeeded.

Instead, the backend asks the blockchain.

That's exactly what we'll build.

---

# Step 1 — Install the XRPL SDK

The official JavaScript SDK makes interacting with the XRP Ledger remarkably simple.

Install it using npm.

```bash
npm install xrpl
```

Then import it into your project.

```javascript
const xrpl = require("xrpl");
```

That's all the setup you need.

---

# Step 2 — Connect to the XRP Ledger

Unlike REST APIs, XRPL communicates through WebSockets.

Creating a connection looks like this.

```javascript
const client = new xrpl.Client(
    "wss://s.altnet.rippletest.net:51233"
);

await client.connect();
```

You're now connected directly to the blockchain.

Think of this WebSocket as your application's gateway into the decentralized ledger.

Every transaction you verify comes directly from the network itself.

> **Pro Tip**
>
> In production, avoid opening a new WebSocket connection for every request. Instead, reuse a shared client instance and automatically reconnect if the connection drops. This significantly improves performance under heavy traffic.

---

# Step 3 — Retrieve a Transaction

Suppose your frontend sends a transaction hash after the user completes payment.

Your backend can retrieve the transaction using a single request.

```javascript
const response = await client.request({
    command: "tx",
    transaction: txHash
});
```

The response contains everything recorded on-chain:

* Sender
* Recipient
* Amount
* Ledger index
* Metadata
* Transaction result

A simplified response looks like this.

```javascript
{
    result: {
        Destination: "...",
        Amount: "25000000",
        meta: {
            TransactionResult: "tesSUCCESS"
        }
    }
}
```

If the transaction hash doesn't exist, the request throws an error.

Always protect blockchain requests with `try...catch`.

```javascript
try {

   // Query blockchain

}
catch(error){

   console.log(error.message);

}
```

Good error handling is just as important as good validation.

---

# Step 4 — Confirm the Transaction Actually Succeeded

Finding a transaction isn't enough.

Some transactions exist on the ledger but failed to execute.

XRPL stores the execution result inside the transaction metadata.

```javascript
const success =
    tx.meta.TransactionResult === "tesSUCCESS";
```

If the result isn't `tesSUCCESS`, reject it immediately.

```javascript
if(!success){

    return {

        verified:false,
        reason:"Transaction failed"

    };

}
```

This simple check prevents your application from accepting unsuccessful payments.

---

# Step 5 — Verify the Destination Wallet

Here's a surprisingly common attack.

Someone copies a legitimate transaction from the blockchain.

The transaction is completely real.

The payment succeeded.

But...

It was sent to **someone else's wallet**.

If your backend only checks whether the transaction exists, the attacker wins.

Always compare the destination address.

```javascript
if(tx.Destination !== expectedRecipient){

    return{

        verified:false,
        reason:"Wrong recipient"

    };

}
```

Only payments sent to your own wallet should be accepted.

---

# Step 6 — Verify the Amount

XRPL stores XRP using its smallest unit called **drops**.

```
1 XRP = 1,000,000 drops
```

Convert the amount into XRP.

```javascript
const paidAmount =
xrpl.dropsToXrp(tx.Amount);
```

Now compare it with the expected payment.

```javascript
if(parseFloat(paidAmount)
<
parseFloat(expectedAmount)){

    return{

        verified:false,
        reason:"Insufficient payment"

    };

}
```

Using a **greater than or equal to** comparison is generally better than requiring an exact match.

Customers occasionally overpay because of wallet rounding or manual entry.

---

# Step 7 — Decode Transaction Memos

Many developers overlook one of XRPL's most useful features: transaction memos.

Instead of maintaining complicated lookup tables, you can include identifiers directly inside blockchain transactions.

For example:

* Order ID
* Customer ID
* Invoice Number
* Subscription ID

XRPL stores memo data as hexadecimal.

```javascript
const memo =
tx.Memos[0].Memo.MemoData;
```

Decode it.

```javascript
const decoded =
Buffer.from(
memo,
"hex"
).toString("utf8");
```

Output:

```
ORDER-10384
```

This makes it incredibly easy to match blockchain payments with records in your own database.

---

# Putting Everything Together

Below is the complete verification function.

```javascript
const xrpl = require('xrpl');

async function verifyPayment(txHash, expectedRecipient, expectedAmountXrp) {
  const client = new xrpl.Client('wss://s.altnet.rippletest.net:51233');
  await client.connect();

  try {
    const response = await client.request({
      command: 'tx',
      transaction: txHash
    });

    const tx = response.result;

    const isSuccess =
      tx.meta.TransactionResult === 'tesSUCCESS';

    if (!isSuccess)
      return {
        verified: false,
        reason: 'Transaction failed'
      };

    if (tx.Destination !== expectedRecipient)
      return {
        verified: false,
        reason: 'Wrong recipient'
      };

    const actualAmount =
      xrpl.dropsToXrp(tx.Amount);

    if (
      parseFloat(actualAmount) <
      parseFloat(expectedAmountXrp)
    )
      return {
        verified: false,
        reason: 'Insufficient amount'
      };

    return {
      verified: true,
      amount: actualAmount
    };

  } catch (error) {

    return {
      verified: false,
      error: error.message
    };

  } finally {

    await client.disconnect();

  }
}
```

Although compact, this function performs the three most important blockchain verification checks:

* ✔ Transaction succeeded
* ✔ Payment reached the correct wallet
* ✔ Correct amount was received

That's enough to securely verify most XRP payments.

---

# Production Tips

Before deploying your payment service, consider adding a few extra safeguards:

* Cache and reuse your XRPL client connection.
* Ensure the transaction is included in a validated ledger.
* Reject duplicate transaction hashes to prevent replay attacks.
* Store successful verification logs for auditing.
* Retry temporary network failures with exponential backoff.
* Validate transaction hashes before querying the ledger.

Small improvements like these dramatically increase the reliability of production payment systems.

---

# Final Thoughts

The biggest lesson in blockchain development is surprisingly simple:

> **Never trust the client. Trust the blockchain.**

Your frontend exists to improve user experience.

Your backend exists to verify the truth.

By querying the XRP Ledger directly, validating transaction success, confirming the destination wallet, checking the payment amount, and decoding transaction metadata, you create a payment verification engine that's both secure and reliable.

Whether you're building a marketplace, subscription platform, gaming application, NFT service, or enterprise payment gateway, these verification steps form the foundation of every trustworthy Web3 backend.

Happy coding!
