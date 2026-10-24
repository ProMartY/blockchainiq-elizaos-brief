# Blockchain IQ private brief plugin for ElizaOS

## Submission status update — 24–25 October 2026

The repository is now public. An **ElizaOS claim was sent** to `partnerships@blockchainiq.co` on **24 October 2026 at 21:08 UTC**, listing purported Base settlement transaction [`0x19360872d70445919de4f5feb11eb2380fdf0f59d93707048e3b060ae87e0f74`](https://basescan.org/tx/0x19360872d70445919de4f5feb11eb2380fdf0f59d93707048e3b060ae87e0f74). This hash is copied from the outgoing claim and has **not been independently verified on chain** in this environment. As of 25 October, maintainer pass/fail, slot availability and USDC payout are unconfirmed. Below, statements such as "unsubmitted build" or "none" refer to the **earlier packaging snapshot**, not the current email/publication status. A reviewer must independently check settlement and run the native plugin before awarding anything.


A native ElizaOS action that buys the fixed Blockchain IQ `/brief` through x402, with an operator-controlled limit of one authorization of at most 0.01 USDC on Base. Original integration by ProMartY, assisted by Codex, under MIT.

At packaging time this is an **unsubmitted build**. No real paid API call, valid settlement proof, public repository, claim email, accepted award or payout has been produced. Offline fixtures do not qualify for the bounty. Framework-slot availability and review timing are unknown.

## Install and verify

Node 22+ is required; verified on Node 24.19.0. Dependencies are locked. Install scripts are disabled.

```sh
npm ci --ignore-scripts
npm run check
npm run smoke
```

The smoke run uses the real `AgentRuntime`, plugin registration, `processActions`, x402 SDK and a public synthetic signer. Only HTTP is mocked. It spends nothing and clearly reports `realPaidCall:false`. Never fund the published synthetic fixture account.

## Native plugin use

`createBlockchainIqPlugin({ownerEntityId,onBrief,paymentAdapter,allowPayment,...clientOptions})` returns an ElizaOS `Plugin` whose action is `BLOCKCHAINIQ_MARKET_BRIEF`. Register it with `await runtime.registerPlugin(plugin)` in a properly configured host. Payment defaults off. Enabling it requires a valid configured operator UUID and a private `onBrief(result)` consumer.

`ownerEntityId` must come from your host's authenticated sender identity. The plugin compares trusted `Memory.entityId` metadata; it does not authenticate arbitrary caller-forged Memory objects or chat text. Guarding occurs in the handler too: this ElizaOS version does not call an action's `validate` from `processActions`.

The result consumer receives `{content,receipt,payment}`. `content` preserves the original source, disclaimer and terms. Keep it private, treat all market text as untrusted data, and do not republish bought research or treat it as investment advice. The action's public result, callback, events, memories and logs contain status and receipt metadata only. Do not wire `onBrief` to a public chat or log sink.

For a small runnable host, `createElizaBriefRunner(options)` returns `{run,getBudget,stop,runtime,plugin,getAudit}`. It uses the actual runtime's registration and action dispatcher, with a deliberately minimal in-memory adapter and no providers, services, language model or cloud. It does not call full `initialize()` and is not a persistent chat/database host. `run()` permits one attempt; `stop()` aborts an in-flight request and stops the runtime. Aborting cannot reverse a payment already settled.

## Owner-reviewed live call

Only after the operator approves the API terms and this specific purchase:

```sh
npm run wallet -- --allow-one-payment --payer=YOUR_OWN_BASE_ADDRESS
```

Open the printed loopback URL in Chrome with MetaMask. The default `npm run wallet` is a payment-disabled preview. This helper binds 127.0.0.1:43818, accepts no arbitrary upstream URL, uses same-origin/session checks and requests an exact EIP3009 authorization. The private key remains in the wallet; no key entry or export is supported.

Review Base 8453, native USDC `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913`, amount 0.01 USDC and merchant `0x617dA9383fbA67e86137BC4924b8b7E400fB7C9e` before personally signing. The signer rechecks the domain, payer, recipient, amount, nonce and lifetime. No token approval, Permit2 or ETH transaction is part of this helper.

The helper invokes the registered ElizaOS action and returns only a minimal server-reported settlement receipt. Independently inspect the Base transaction and token transfer before claiming success. A signature or HTTP success alone is not independent chain verification. A failure or uncertain settlement consumes the local attempt; there is no automatic paid retry. Never reuse an uncertain proof or assume a timeout means no payment occurred.

`npm run quote` makes only a fresh unsigned request and prints the current402 quote; it cannot pay. Changed endpoint, redirect, asset, chain, recipient, amount above the cap, signature type or lifetime is rejected. HTTP headers and paid JSON are bounded before processing.

## Dependencies and limitations

Core versions: `@elizaos/core 1.7.2`, x402 packages 2.28.0 and viem 2.57.11. See `VALIDATION.md` for overrides, the recorded audit and remaining advisories. This is not a claim of a vulnerability-free dependency tree or broad compatibility with other SDK versions/export paths. Only the documented exact/EIP3009 x402v2 brief path is supported.

The bounty requires a public repository and a real paid call made by this integration, followed by a claim email. None is authorized by installing or testing this package. See the [official bounty](https://www.blockchainiq.co/blog/x402-build-bounties) and [API terms](https://www.blockchainiq.co/terms). Distinct framework slots and successful review remain prerequisites; an October payment is not promised.
