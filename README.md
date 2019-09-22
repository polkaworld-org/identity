# identity

Substrate-based Onchain Identity and Access Management.

# Design

## Principles

 Human readable identifiers as first class citizens, making self-soveriegn-identity come true.

## Modules

### univesal_name_service

 Decouple human readable identifiers from low-level interpretor, by using universal_register, universal_resolver

1. datatype
    1. identifier: asRef&lt;u8&gt;
        
        ``` 
        eg. helloworld.id, transfer.helloworld.id, approver.helloworld.id, wallet.eth, file.ipfs, etc.
        ``` 

    2. register: Trait
        
        ``` 
        eg. identity.register, authorization.identitfy.register, profile.identify.register
        ``` 
 
    3. resolver: Trait

        ``` 
        eg. identity.resolver, authorization.identitfy.resolver, profile.identify.resolver,  eth.resolver, ipfs.resolver
        ``` 

2. storage
    1. map: identifier => resolversArray

3. event

    1. IdentityRegistered

4. module
    1. register
    2. resolver

### identity (as a resolver)

1. datatype
    1. identifier: asRef&lt;u8&gt;
    2. role struct {threshold, keysArray, permissionsArray}
        
        ``` 
        struct {
            threshold: 2;
            keys: [
                {key: key1, weight: 1},
                {key: key2, weight: 1},
                {key: key3, weight: 1},
            ],
            roles: [
                {role: role1, weight: 1},
                {role: role2, weight: 1},
            ]
        }
        ``` 

    3. resource: module
    4. actions: function

2. storage
    1. Role: map: identifier -> role;
    2. Permissions: identifer -> grantArray

3. event
    1. RoleRegistered
    2. RoleUpdated
    3. PerimissionGranted
    4. PermissionRevoked

4. module
    1. register_role
    2. update_role
    3. grant_permission
    4. revoke_permission
    5. check_permission

### balancex

1. datatype

2. storage

3. event

4. module



### authorization
1. authorization


# Building

Install Rust:

```bash
curl https://sh.rustup.rs -sSf | sh
```

Install required tools:

```bash
./scripts/init.sh
```

Build the WebAssembly binary:

```bash
./scripts/build.sh
```

Build all native code:

```bash
cargo build
```

# Run

You can start a development chain with:

```bash
cargo run -- --dev
```

Detailed logs may be shown by running the node with the following environment variables set: `RUST_LOG=debug RUST_BACKTRACE=1 cargo run -- --dev`.

If you want to see the multi-node consensus algorithm in action locally, then you can create a local testnet with two validator nodes for Alice and Bob, who are the initial authorities of the genesis chain that have been endowed with testnet units. Give each node a name and expose them so they are listed on the Polkadot [telemetry site](https://telemetry.polkadot.io/#/Local%20Testnet). You'll need two terminal windows open.

We'll start Alice's substrate node first on default TCP port 30333 with her chain database stored locally at `/tmp/alice`. The bootnode ID of her node is `QmQZ8TjTqeDj3ciwr93EJ95hxfDsb9pEYDizUAbWpigtQN`, which is generated from the `--node-key` value that we specify below:

```bash
cargo run -- \
  --base-path /tmp/alice \
  --chain=local \
  --alice \
  --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
  --telemetry-url ws://telemetry.polkadot.io:1024 \
  --validator
```

In the second terminal, we'll start Bob's substrate node on a different TCP port of 30334, and with his chain database stored locally at `/tmp/bob`. We'll specify a value for the `--bootnodes` option that will connect his node to Alice's bootnode ID on TCP port 30333:

```bash
cargo run -- \
  --base-path /tmp/bob \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/QmQZ8TjTqeDj3ciwr93EJ95hxfDsb9pEYDizUAbWpigtQN \
  --chain=local \
  --bob \
  --port 30334 \
  --telemetry-url ws://telemetry.polkadot.io:1024 \
  --validator
```

Additional CLI usage options are available and may be shown by running `cargo run -- --help`.
