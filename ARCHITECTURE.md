# 🏛️ ScrollVerse Platform Architecture

**بِسْمِ اللَّهِ الرَّحْمَٰنِ الرَّحِيمِ 🕋**

This document describes the technical architecture of the ScrollVerse platform — how all repositories connect, what each layer is responsible for, and how data flows from user interaction down to the blockchain.

---

## System Architecture Overview

```
╔══════════════════════════════════════════════════════════════════════════╗
║                    SCROLLVERSE SOVEREIGN PLATFORM                        ║
║                  بِسْمِ اللَّهِ الرَّحْمَٰنِ الرَّحِيمِ                ║
╚══════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────┐
│                         USER / BROWSER                                  │
│                   MetaMask  ·  WalletConnect                            │
└────────────────────────────┬────────────────────────────────────────────┘
                             │  HTTPS + WebSocket
                             ▼
┌─────────────────────────────────────────────────────────────────────────┐
│              FRONTEND — chaishillomnitech1/nextjs-boilerplate            │
│                    Deployed on: Vercel                                  │
│                                                                         │
│   app/page.tsx          — Landing page, stats bar, hero section         │
│   app/music/page.tsx    — Featured tracks, royalty display              │
│   app/artists/page.tsx  — Artist registration form                      │
│   app/dao/page.tsx      — DAO proposals, guardian tiers, voting         │
│   app/zakat/page.tsx    — Zakat calculator, distribution history        │
│   app/guardians/page.tsx — Guardian registration and tier board         │
│                                                                         │
│   app/api/guardians/route.ts  — Next.js API proxy → Backend            │
│   app/api/music/route.ts      — Next.js API proxy → Backend            │
│                                                                         │
│   lib/constants.ts  — ARTIST_SHARE=85, ZAKAT_RATE=7.77, SVT_SUPPLY     │
│   lib/types.ts      — Artist, Track, Guardian, Proposal, ZakatRecord   │
│   components/Navbar.tsx   — Navigation with wallet connect button       │
│   components/StatCard.tsx — Reusable stats display component            │
│                                                                         │
│   Tech Stack: Next.js 14 · TypeScript · Tailwind CSS · wagmi · viem    │
└──────────┬──────────────────────────────────────┬───────────────────────┘
           │                                      │
           │  REST API (HTTPS)                    │  wagmi / viem
           │  NEXT_PUBLIC_BACKEND_URL             │  Direct contract reads
           ▼                                      ▼
┌──────────────────────────┐         ┌────────────────────────────────────┐
│  BACKEND ENGINE           │         │   SCROLL SEPOLIA TESTNET           │
│  scrollsoul-music-sync    │         │   Chain ID: 534351                 │
│  Deployed on: Railway     │         │   RPC: sepolia-rpc.scroll.io       │
│                           │         │                                    │
│  src/index.ts             │         │  ┌──────────────────────────────┐  │
│  src/MusicSyncPlatform.ts │         │  │  ScrollVerseToken.sol (SVT)  │  │
│                           │         │  │  ERC-20 · 144M supply        │  │
│  services/                │         │  │  Mintable · Burnable         │  │
│    royaltyService.ts      │◄────────┤  └──────────────────────────────┘  │
│    guardianService.ts     │ ethers  │                                    │
│    zakatScheduler.ts      │  v6     │  ┌──────────────────────────────┐  │
│                           │         │  │  ArtistRoyaltyVault.sol      │  │
│  api/routes/              │         │  │  85% artist                  │  │
│    artists.ts             │         │  │  7.77% Zakat                 │  │
│    royalties.ts           │         │  │  7.23% DAO treasury          │  │
│                           │◄────────┤  └──────────────────────────────┘  │
│  types/index.ts           │ ethers  │                                    │
│                           │  v6     │  ┌──────────────────────────────┐  │
│  models/                  │         │  │  ZakatAutomation.sol         │  │
│  blockchain/              │         │  │  777 basis points (7.77%)    │  │
│  analytics/               │         │  │  Multi-recipient distribution│  │
│  billing/                 │         │  │  Monthly auto-trigger        │  │
│  security/                │         │  └──────────────────────────────┘  │
│                           │         │                                    │
│  Dockerfile               │         │  ┌──────────────────────────────┐  │
│  docker-compose.yml       │◄────────┤  │  GuardianRegistry.sol        │  │
│                           │ ethers  │  │  144,000 target              │  │
│  Port: 3001               │  v6     │  │  Tiers: Seeker → Archangel   │  │
└──────────────────────────┘         │  │  isGuardian(address) view     │  │
                                     │  └──────────────────────────────┘  │
                                     │                                    │
                                     │  ┌──────────────────────────────┐  │
                                     │  │  RoseGoldEncryption.sol      │  │
                                     │  │  RoseGoldEncryptionEnhanced  │  │
                                     │  └──────────────────────────────┘  │
                                     └────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│          SMART CONTRACT REPOSITORY                                      │
│          chaishillomnitech1/londyn-legacy-protocol                      │
│                                                                         │
│   contracts/ArtistRoyaltyVault.sol    — Royalty distribution vault      │
│   contracts/ScrollVerseToken.sol      — SVT ERC-20 token                │
│   contracts/ZakatAutomation.sol       — Automated Zakat distribution    │
│   contracts/GuardianRegistry.sol      — Guardian node registry          │
│   contracts/RoseGoldEncryption.sol    — Encryption layer (existing)     │
│                                                                         │
│   hardhat.config.js   — Scroll Sepolia network config (chainId 534351) │
│   scripts/deploy.js   — Deploys all 4 contracts in order               │
│   test/contracts.test.js — Hardhat/Mocha test suite                    │
│   package.json        — hardhat + @openzeppelin/contracts + ethers      │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│          ARCHIVE & ROADMAP HUB                                          │
│          chaishillomnitech1/scrollverse-first-knight-covenant           │
│                                                                         │
│   SCROLLVERSE-BUILD-ROADMAP.md  — Week-by-week technical build plan     │
│   ARCHITECTURE.md               — This file                             │
│   README.md                     — Repository overview                   │
│   SMART-CONTRACTS-DEPLOYED.md  — Deployed contract reference           │
│   FIRST-KNIGHT-COVENANT.md     — Founding covenant                      │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Data Flow Diagrams

### Artist Registration Flow

```
User fills form                Artist data stored
on /artists page               in backend database
      │                               │
      ▼                               ▼
nextjs-boilerplate          scrollsoul-music-sync
POST /api/artists/register ────────────────────►
                            POST /artists/register
                                       │
                                       │ (optional: on-chain record)
                                       │
                                       ▼
                            londyn-legacy-protocol
                            GuardianRegistry.sol
                            or ArtistRoyaltyVault.sol
```

### Royalty Distribution Flow

```
Streaming revenue
collected off-chain
        │
        ▼
scrollsoul-music-sync
royaltyService.ts
calculateRoyaltySplit(amount)
        │
        ├──► 85% ──► Artist wallet (direct transfer)
        │
        ├──► 7.77% ──► ZakatAutomation.sol
        │                      │
        │                      ├──► Charity 1 (40% of Zakat)
        │                      ├──► Charity 2 (30% of Zakat)
        │                      └──► Artist Fund (30% of Zakat)
        │
        └──► 7.23% ──► DAO Treasury wallet
```

### Guardian Registration Flow

```
User on /dao page
clicks "Register as Guardian"
        │
        ▼
wagmi useWriteContract
        │
        ▼ on-chain tx
GuardianRegistry.sol
registerGuardian(address, tier)
        │
        ├──► Emits GuardianRegistered event
        │
        ▼
scrollsoul-music-sync
blockchainListener.ts
(listens for GuardianRegistered events)
        │
        ▼
Database: stores guardian record
        │
        ▼
nextjs-boilerplate
/api/guardians/route.ts
        │
        ▼
app/page.tsx stats bar
"Guardian Count: X / 144,000"
```

### Zakat Distribution Flow

```
Monthly cron job (1st of month, 00:00 UTC)
zakatScheduler.ts in scrollsoul-music-sync
        │
        ▼
ZakatAutomation.sol
distributeZakat()
        │
        ├──► Splits ETH balance by recipient sharePercent
        ├──► Emits ZakatDistributed event
        │
        ▼
nextjs-boilerplate
app/zakat/page.tsx
queryFilter(ZakatDistributed events)
        │
        ▼
Distribution history table rendered
```

---

## Repository Map

```
chaishillomnitech1/
│
├── nextjs-boilerplate/          ◄─── FRONTEND (Vercel)
│   ├── app/
│   │   ├── page.tsx             Landing page
│   │   ├── music/page.tsx       Music platform
│   │   ├── artists/page.tsx     Artist registration
│   │   ├── dao/page.tsx         DAO + guardian tiers
│   │   ├── zakat/page.tsx       Zakat calculator
│   │   └── api/
│   │       ├── guardians/route.ts
│   │       └── music/route.ts
│   ├── lib/
│   │   ├── constants.ts         Platform constants
│   │   └── types.ts             Shared TypeScript types
│   └── components/
│       ├── Navbar.tsx
│       └── StatCard.tsx
│
├── scrollsoul-music-sync/       ◄─── BACKEND ENGINE (Railway)
│   └── src/
│       ├── MusicSyncPlatform.ts
│       ├── services/
│       │   ├── royaltyService.ts
│       │   ├── guardianService.ts
│       │   └── zakatScheduler.ts
│       ├── api/routes/
│       │   ├── artists.ts
│       │   └── royalties.ts
│       └── types/index.ts
│
├── londyn-legacy-protocol/      ◄─── SMART CONTRACTS (Scroll Sepolia)
│   ├── contracts/
│   │   ├── ArtistRoyaltyVault.sol
│   │   ├── ScrollVerseToken.sol
│   │   ├── ZakatAutomation.sol
│   │   ├── GuardianRegistry.sol
│   │   ├── RoseGoldEncryption.sol
│   │   └── RoseGoldEncryptionEnhanced.sol
│   ├── scripts/deploy.js
│   ├── test/contracts.test.js
│   └── hardhat.config.js
│
└── scrollverse-first-knight-covenant/  ◄─── ARCHIVE & ROADMAP HUB
    ├── SCROLLVERSE-BUILD-ROADMAP.md    This repository
    ├── ARCHITECTURE.md
    └── *.md                            Covenant archives
```

---

## Technology Stack

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| Frontend Framework | Next.js | 14 (App Router) | TypeScript, Tailwind CSS |
| Frontend Wallet | wagmi + viem | latest | Scroll Sepolia chain config |
| Backend Runtime | Node.js | ≥18 | TypeScript compiled |
| Backend Framework | Express.js | ^4 | REST API |
| Smart Contracts | Solidity | ^0.8.19 | OpenZeppelin v5 |
| Contract Framework | Hardhat | ^2.19 | Scroll Sepolia network |
| Token Standard | ERC-20 | OpenZeppelin | ScrollVerseToken (SVT) |
| Frontend Hosting | Vercel | — | Auto-deploy from GitHub main |
| Backend Hosting | Railway | — | Docker or Node buildpack |
| Blockchain | Scroll Sepolia | Chain ID 534351 | Testnet → Mainnet later |
| Contract Verification | Scrollscan | — | Source code verified |

---

## Security Layers

```
┌─────────────────────────────────────────────────┐
│              Security Architecture               │
│                                                  │
│  Smart Contracts                                 │
│  ├── OpenZeppelin ReentrancyGuard on all        │
│  │   contracts that transfer ETH/tokens         │
│  ├── OpenZeppelin Ownable — admin functions      │
│  │   (mint, addRecipient, setZakatRate)          │
│  └── Input validation on all public functions   │
│                                                  │
│  Backend                                         │
│  ├── JWT authentication on protected routes     │
│  ├── Private key stored in environment variable │
│  ├── Rate limiting on all API endpoints         │
│  └── Input sanitization before DB writes        │
│                                                  │
│  Frontend                                        │
│  ├── Wallet connect — user signs own txs        │
│  ├── No private keys in browser                 │
│  └── Environment variables for contract         │
│      addresses (not secrets)                    │
└─────────────────────────────────────────────────┘
```

---

## Deployment Environments

| Environment | Frontend URL | Backend URL | Network |
|-------------|-------------|-------------|---------|
| Development | http://localhost:3000 | http://localhost:3001 | Scroll Sepolia |
| Production | https://scrollverse.vercel.app | https://scrollsoul-music-sync.up.railway.app | Scroll Sepolia → Mainnet |

---

**بِسْمِ اللَّهِ — The architecture is clear. The foundation is solid. Build. 🕋⚡️**
