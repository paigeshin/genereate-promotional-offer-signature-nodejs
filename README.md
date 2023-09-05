# genereate-promotional-offer-signature-nodejs

```jsx
const express = require('express');
const bodyParser = require('body-parser');
const crypto = require('crypto');

const app = express();
const port = 3000;

// Middleware to parse JSON requests
app.use(bodyParser.json());

// Your key ID and private key from App Store Connect
const keyID = "YOUR_KEY_ID";
const privateKeyPEM = `-----BEGIN PRIVATE KEY-----
YOUR_PRIVATE_KEY_CONTENT_HERE
-----END PRIVATE KEY-----`;

app.get('/offer', (req, res) => {
    const { appBundleID, productIdentifier, offerID, applicationUsername } = req.body;

    if (!appBundleID || !productIdentifier || !offerID || !applicationUsername) {
        return res.status(400).send({ error: 'Missing required fields' });
    }

    // Create the payload
    const payload = {
        appBundleID,
        productIdentifier,
        offerID,
        applicationUsername,
        nonce: crypto.randomBytes(16).toString('hex'),
        timestamp: Math.floor(Date.now() / 1000)
    };

    // Convert the payload to a string
    const payloadString = JSON.stringify(payload);

    // Create the signature
    const sign = crypto.createSign('sha256');
    sign.update(payloadString);
    sign.end();
    const signature = sign.sign(privateKeyPEM, 'base64');

    // Send the response
    res.send({
        payload: payloadString,
        signature
    });
});

app.listen(port, () => {
    console.log(`Server running on http://localhost:${port}`);
});

```
