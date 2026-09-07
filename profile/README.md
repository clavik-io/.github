<div align="center">

# Clavik

**Open-source, multi-tenant vault for secrets, keys, and credentials.**

Store, manage, and access API keys, tokens, certificates, and cryptographic keys — with tenant isolation, RBAC, and audit logging built in.

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Website](https://img.shields.io/badge/website-clavik.io-brightgreen)](https://clavik.io)

</div>

---

## What is Clavik?

Clavik is a vault and secret management platform that gives teams a single place to:

- **Store secrets** — passwords, API keys, certificates, SSH keys, and generic credentials
- **Manage cryptographic keys** — generate, rotate, encrypt/decrypt, sign/verify with AES-GCM, RSA, ECDSA, and HMAC
- **Organise with folders & RBAC** — hierarchical folders with fine-grained, role-based permissions
- **Isolate tenants** — every tenant gets its own encryption keys and namespace
- **Audit everything** — full activity log for compliance and forensics

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                     vault-ui                        │
│                   (Angular SPA)                     │
└──────────────────────┬──────────────────────────────┘
                       │ HTTPS
┌──────────────────────▼──────────────────────────────┐
│                    vault-srv                        │
│          REST (gRPC-Gateway) + gRPC API             │
│                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │
│  │  kms-core   │  │  kms-store  │  │   Cidaas   │  │
│  │ (crypto ops)│  │(Raft + DB)  │  │  (OAuth2)  │  │
│  └─────────────┘  └─────────────┘  └────────────┘  │
└─────────────────────────────────────────────────────┘
         ▲               ▲
         │               │
   ┌─────┴─────┐   ┌────┴─────┐
   │ vault-proto│   │ RethinkDB│
   │  (protobuf)│   │          │
   └───────────┘   └──────────┘
```

## Repositories

### Core

| Repository | Description |
|---|---|
| **vault-srv** | Go backend — REST & gRPC APIs, multi-tenant secret and key management |
| **vault-ui** | Angular frontend — web dashboard for managing secrets, keys, and folders |
| **vault-proto** | Protocol Buffer definitions and generated Go code |
| **vault-client** | Legacy Go client library for Vault KMS (JWT, JWE, JWK operations) — see **clavik-go** for the official SDK |

### Crypto & Storage

| Repository | Description |
|---|---|
| **kms-core** | Cryptographic core — hybrid high-level/low-level API for key and secret operations |
| **kms-store** | Distributed key & secret storage with dual Raft clusters (Dragonboat) |

### SDKs

| SDK | Language | Package |
|---|---|---|
| **clavik-go** | Go | `github.com/clavik-io/clavik-go` |
| **clavik-js** | TypeScript / JavaScript | `@clavik/vault-sdk` |
| **clavik-py** | Python | `vault-sdk-python` |

All SDKs support API key, bearer token, and OAuth2 client-credentials authentication with automatic retry and exponential backoff.

### Tooling

| Repository | Description |
|---|---|
| **clavik-docs** | Documentation site (Docusaurus) |
| **local-clavik** | Local development infra — Nginx, RethinkDB, TLS certs |

## Quick Start

### Install an SDK

```bash
# Go
go get github.com/clavik-io/clavik-go

# JavaScript / TypeScript
npm install @clavik/vault-sdk

# Python
pip install vault-sdk-python
```

### Create a secret (TypeScript example)

```typescript
import { VaultClient } from "@clavik/vault-sdk";

const client = new VaultClient({
  endpoint: "https://api.clavik.io/api/v1",
  apiKey: "vk_live_...",
  tenantKey: "my-account",
});

const secret = await client.secrets.create({
  name: "prod-db",
  secret_type: "SECRET_TYPE_PASSWORD",
  value: btoa(JSON.stringify({ username: "admin", password: "s3cret" })),
});
```

### Encrypt data (Go example)

```go
ct, err := client.Keys().Encrypt(ctx, vault.EncryptRequest{
    KeyID: encKey.ID,
    Data:  "base64-encoded-plaintext",
})
```

See the [full documentation](https://clavik.io) for guides, API reference, and self-hosting instructions.

## Key Features

| Feature | Details |
|---|---|
| **Secret Management** | CRUD, versioning, rotation, folder-based organisation |
| **Key Management** | Generation, rotation, encrypt/decrypt, sign/verify |
| **Algorithms** | AES-GCM · RSA (RS256/384/512) · ECDSA (ES256/384/512) · HMAC (HS256/384/512) |
| **Multi-Tenancy** | Per-tenant encryption keys, namespace isolation |
| **Auth** | OAuth2/OIDC (Cidaas), API keys, bearer tokens |
| **RBAC** | Folder-level permissions with role-based access control |
| **Audit Logging** | Full activity log with compliance reporting |
| **Distributed Storage** | Dual Raft clusters for keys and secrets (strong consistency) |
| **Crypto Tools** | Random bytes, hashing, key wrapping/unwrapping |

## Contributing

We welcome contributions! To get started:

1. Fork the relevant repository
2. Create a feature branch (`git checkout -b feat/my-feature`)
3. Follow [conventional commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, etc.)
4. Add tests for new functionality
5. Open a pull request with a clear description

See each repository's README for build instructions and development setup.

## License

This organisation's repositories are released under the [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0) unless stated otherwise in the individual repository.

---

<div align="center">

**[Website](https://clavik.io)** · **[Documentation](https://clavik.io)** · **[Report an Issue](https://github.com/clavik-io/.github/issues)**

</div>
