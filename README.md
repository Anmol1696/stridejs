# stridejs

<img src="https://user-images.githubusercontent.com/545047/190012774-f9886f76-246a-4f45-b21a-f2798f74f9f8.png" width="400">

## install

```sh
npm install stridejs
```

## Connecting with Wallets and Signing Messages

⚡️ For web interfaces, we recommend using [cosmos-kit](https://github.com/cosmology-tech/cosmos-kit). To sign and broadcast messages, you can create signers with a variety of options:

- [cosmos-kit](https://github.com/cosmology-tech/cosmos-kit/tree/main/packages/react#signing-clients) (recommended)
- [keplr](https://docs.keplr.app/api/cosmjs.html)
- [cosmjs](https://gist.github.com/webmaster128/8444d42a7eceeda2544c8a59fbd7e1d9)

### Initializing the Stargate Client

We recommend manually making the `SigningStargateClient` instance yourself by using `getSigningStrideClientOptions`:

```ts
import {
  getSigningStrideClientOptions,
  strideAccountParser,
} from "@stride/proto";

const { registry, aminoTypes } = getSigningStrideClientOptions();

const client = await SigningStargateClient.connectWithSigner(
  rpc,
  offlineSigner,
  {
    registry,
    aminoTypes,
    accountParser: strideAccountParser,
  }
);
```

## Usage

We strongly recommend that you check the generated files in `src/codegen/stride` and use it as source of truth for which functions you could use.

The rest of our documentation will cover only the tip of the iceberg &mdash; examples you can take ideas from.

### RPC Client

```ts
import { stride } from "stridejs";

const client = await stride.ClienFactory.createRPCQueryClient({
  rpcEndpoint: RPC_ENDPOINT,
});

const balance = await client.cosmos.bank.v1beta1.allBalances({
  address: "stride1addresshere",
});
```

### Composing & Broadcasting Stride Messages

```ts
import { stride } from "stridejs";

const msgClaimFreeAmount =
  stride.claim.MessageComposer.withTypeUrl.claimFreeAmount({
    user: "stride1addresshere",
  });

const fee = {
  amount: [
    {
      amount: "0",
      denom: "STRD",
    },
  ],
  gas: 250_000,
};

const tx = await strideAccount.client.signAndBroadcast(
  "stride1addresshere",
  [msgClaimFreeAmount],
  fee,
  ""
);

assertIsDeliverTxSuccess(tx);
```

If you're unfamiliar with Stargate, you can read their guide [here](https://gist.github.com/webmaster128/8444d42a7eceeda2544c8a59fbd7e1d9).

#### Composing IBC Messages

```js
import { ibc } from "stridejs";

const { transfer } =
  ibc.applications.transfer.v1.MessageComposer.withTypeUrl.transfer({
    // Redacted (check internal types for the message parameters)
  });
```

## Developing & Publishing

When first cloning the repo:

```bash
git submodule update --init
npm install
```

### Codegen

Update the generated ts files:

```bash
npm run codegen
```

Build the module and types:

```bash
npm run buidl
```

### Publishing

If you haven't logged to npm cli, run:

```bash
npm login
```

Afterwards, update package.json version.

```bash
# Example: <version> = v0.4.1
git tag <version>
git push origin <version>
git push origin main
npm publish
```

## Credits

🛠 Built by Cosmology — if you like our tools, please consider delegating to [our validator ⚛️](https://cosmology.tech/validator)

Code built with the help of these related projects:

- [@cosmwasm/ts-codegen](https://github.com/CosmWasm/ts-codegen) for generated CosmWasm contract Typescript classes
- [@osmonauts/telescope](https://github.com/osmosis-labs/telescope) a "babel for the Cosmos", Telescope is a TypeScript Transpiler for Cosmos Protobufs.
- [cosmos-kit](https://github.com/cosmology-tech/cosmos-kit) A wallet connector for the Cosmos ⚛️
