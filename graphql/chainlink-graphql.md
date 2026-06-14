# Chainlink GraphQL API

## Description

Chainlink exposes a GraphQL API as part of its node operator interface — the same API powering the Chainlink Operator UI (formerly Chainlink Dashboard). It provides programmatic access to node management operations: creating and monitoring jobs, managing cryptographic key bundles, configuring bridges to external adapters, inspecting on-chain transactions, and administering the Feeds Manager (OCR/OCR2 job proposals from the Feeds Manager service).

This is distinct from the deprecated Subgraph endpoints that were previously available via The Graph Network for price-feed data. The node GraphQL API is the canonical, actively maintained interface for interacting with a Chainlink node.

## Endpoint

Each Chainlink node exposes its own GraphQL endpoint locally. There is no global shared endpoint — operators query their own node instance.

```
https://<your-chainlink-node-host>/query
```

The default local development URL is:

```
http://localhost:6688/query
```

## Authentication

Session-based authentication. You must first authenticate via the REST login endpoint to obtain a session cookie, then include that cookie with GraphQL requests.

```
POST /sessions
Content-Type: application/json

{"email": "user@example.com", "password": "yourpassword"}
```

The response sets a `clsession` cookie. Include this cookie in subsequent GraphQL requests:

```
POST /query
Cookie: clsession=<session-token>
Content-Type: application/json
```

API tokens (created via the `createAPIToken` mutation) may also be used as an alternative to session cookies for programmatic access.

## Schema Source

Schema sourced from the official Chainlink open-source repository:

- Repository: https://github.com/smartcontractkit/chainlink
- Schema path: `core/web/schema/schema.graphql` and `core/web/schema/type/`
- Branch: `develop`

## Key Operations

### Queries

| Query | Description |
|-------|-------------|
| `job(id)` / `jobs` | Fetch job definitions and their specs |
| `jobRun(id)` / `jobRuns` | Fetch pipeline run results |
| `bridge(id)` / `bridges` | External adapter bridges |
| `chain(id)` / `chains` | EVM chain configurations |
| `node(id)` / `nodes` | RPC node configurations |
| `ethKeys` | Ethereum key addresses managed by the node |
| `ethTransactions` | On-chain transaction history |
| `feedsManager(id)` / `feedsManagers` | Feeds Manager connections (OCR job distribution) |
| `vrfKeys` | VRF key bundles |
| `ocrKeyBundles` / `ocr2KeyBundles` | OCR/OCR2 signing key bundles |
| `p2pKeys` | P2P networking keys |
| `csaKeys` | CSA (Chainlink Service Agreement) keys |
| `features` | Feature flags enabled on this node |
| `configv2` | Node configuration (v2 TOML format) |
| `globalLogLevel` | Current log verbosity level |

### Mutations

| Mutation | Description |
|----------|-------------|
| `createJob` / `deleteJob` / `runJob` | Job lifecycle management |
| `createBridge` / `updateBridge` / `deleteBridge` | Bridge adapter management |
| `createFeedsManager` / `updateFeedsManager` | Feeds Manager configuration |
| `approveJobProposalSpec` / `rejectJobProposalSpec` | OCR job proposal workflow |
| `createOCRKeyBundle` / `createOCR2KeyBundle` | Key generation |
| `createVRFKey` / `deleteVRFKey` | VRF key management |
| `createAPIToken` / `deleteAPIToken` | Programmatic access tokens |
| `setGlobalLogLevel` | Runtime log level adjustment |
| `updateUserPassword` | Operator account management |

## Notes

- The Chainlink Operator UI (https://github.com/smartcontractkit/operator-ui) is the reference client implementation for this GraphQL API.
- Job specifications are submitted as TOML strings via `CreateJobInput.TOML` — the GraphQL layer wraps the TOML job spec format.
- The Feeds Manager enables decentralized job distribution via the Chainlink Feeds Manager service, used primarily for OCR and OCR2 price feed jobs across the DON (Decentralized Oracle Network).
- CCIP (Cross-Chain Interoperability Protocol) jobs are supported via the `CCIPSpec` type.
- Price feed subgraphs previously available on The Graph Network (e.g., `smartcontractkit/chainlink-feed-registry`) have been deprecated and are no longer accessible.
