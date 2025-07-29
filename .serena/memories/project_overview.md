# Moonbeam Project Overview

Moonbeam is an Ethereum-compatible parachain built with the Polkadot-SDK. It consists of three main networks:
- Moonbeam (production on Polkadot)
- Moonriver (production on Kusama)
- Moonbase (testnet)

## Key Components
- Runtime implementations in `runtime/moonbeam`, `runtime/moonriver`, `runtime/moonbase`
- Custom pallets in `pallets/`
- Precompiles for EVM compatibility in `precompiles/`
- Node implementation in `node/`
- Client utilities in `client/`

## Current Context
Working on SDK upgrade from polkadot-stable2409 to polkadot-stable2412. Currently fixing runtime API changes errors.