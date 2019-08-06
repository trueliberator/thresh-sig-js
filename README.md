Two Party signatures JS SDK 
=====================================
Javascript SDK (for curve Secp256k1).<br>
Supports:
* Two-Party ECDSA
* Two-Party Schnorr (compatible with [Zilliqa's Schnorr signatures](https://github.com/Zilliqa/Zilliqa-JavaScript-Library/tree/dev/packages/zilliqa-js-crypto))

## Installation:
1. Install [nightly Rust](https://github.com/rust-lang/rustup.rs#installation):
2. Install the package:
```bash
$ npm install @kzen-networks/thresh-sig
```
Alternatively, clone it:
```bash
$ git clone https://github.com/KZen-networks/thresh-sig-js
$ cd thresh-sig-js
$ npm install
$ npm run build
```

## Usage:

Start party one's server 
(acts as the co-signer in the two-party signature scheme):
```js
const { Party1 } = require('@kzen-networks/thresh-sig');
const p1 = new Party1();
p1.launchServer();
```
ECDSA party two:
```js
const { EcdsaParty2 } = require('@kzen-networks/thresh-sig');
const crypto = require('crypto');

const P1_ENDPOINT = 'http://localhost:8000';

(async () => {
    const party2 = new EcdsaParty2(P1_ENDPOINT);
    const party2MasterKeyShare = await party2.generateMasterKey();
    const party2ChildShare = party2.getChildShare(party2MasterKeyShare, 0, 0);
    const msgHash = crypto.createHash('sha256').update('some message').digest();
    const signature = await party2.sign(msgHash, party2ChildShare, 0, 0);
    console.log(JSON.stringify(signature));
    // {"r": <32-bytes-hex>,"s": <32-bytes-hex>,"recid": <0 or 1>}
})();
```

Schnorr party two:
```js
const { SchnorrParty2 } = require('@kzen-networks/thresh-sig');
const crypto = require('crypto');

const P1_ENDPOINT = 'http://localhost:8000';

(async () => {
    const party2 = new SchnorrParty2(P1_ENDPOINT);
    const party2Share = await party2.generateKey();
    const msgHash = crypto.createHash('sha256').update('some message').digest();
    const signature = await party2.sign(msgHash, party2Share);
    console.log(JSON.stringify(signature));
    // {"e": <32-bytes-hex>,"s": <32-bytes-hex>}
})();
```

## Demo:

You can run a command line demo and see the two-party signing protocol in action. <br>
Party one:
```
$ node ./demo/party1.js
🔧 Configured for production.
    => address: 0.0.0.0
    => port: 8000
    => log: critical
    => workers: 24
    => secret key: generated
    => limits: forms = 32KiB
    => keep-alive: 5s
    => tls: disabled
🚀 Rocket has launched from http://0.0.0.0:8000
```
Party two (separate shell) - 
1. ECDSA:
```
$ node ./demo/ecdsa-party2.js
{"r":"b3d6168cc8ab6da64697b9e81c55863078da65cac7bbebc3d3f747dae0c6ac16","s":"449052412e20e510f8d4e31d721b3ef42199a9886a58a058c42d142b5850a177","recid":0}
```
2. Schnorr:
```
$ node ./demo/schnorr-party2.js
{"e":"d960b3fe66d2dc1c2115c93b3e674344d73063c22148ff3bc8d67493ffb19814","s":"ed2ecaa8882ccaac946bf951835033d4326aaff2e1e466ecf1fdda32a6fc762b"}
```

## Contact
Feel free to [reach out](mailto:github@kzencorp.com) or join the KZen Research [Telegram]( https://t.me/kzen_research) for discussions on code and research.
