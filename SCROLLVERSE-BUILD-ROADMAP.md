# 🏗️ ScrollVerse Full Platform Build Roadmap

**بِسْمِ اللَّهِ الرَّحْمَٰنِ الرَّحِيمِ 🕋**

**Status:** Active  
**Last Updated:** July 2026  
**Target Network:** Scroll Sepolia → Scroll Mainnet  
**Repositories:** `londyn-legacy-protocol` · `scrollsoul-music-sync` · `nextjs-boilerplate` · `scrollverse-first-knight-covenant`

---

## Overview

This roadmap transforms the ScrollVerse vision into deployed, running software across five phases spanning ten weeks. Each phase lists the exact files to edit, commands to run, and measurable success criteria.

```
Phase 1  ████████░░░░░░░░░░░░  Smart Contracts
Phase 2  ░░░░████████░░░░░░░░  Backend Engine
Phase 3  ░░░░░░░░████████░░░░  Frontend Portal
Phase 4  ░░░░░░░░░░░░████████  Guardian System
Phase 5  ░░░░░░░░░░░░░░░░████  DAO + Zakat
```

---

## Phase 1 — Foundation: Smart Contracts (Weeks 1–2)

### Objective
Deploy all four core smart contracts to Scroll Sepolia testnet and record their verified addresses.

### Repository
`chaishillomnitech1/londyn-legacy-protocol`

### Prerequisites
- Node.js ≥ 18
- MetaMask wallet funded with Scroll Sepolia ETH (faucet: https://sepolia-faucet.scroll.io/)
- Alchemy or Infura RPC key (or use the public Scroll Sepolia endpoint)

### Step 1 — Install Dependencies

```bash
cd londyn-legacy-protocol
npm install
```

`package.json` must include:
```json
{
  "devDependencies": {
    "hardhat": "^2.19.0",
    "@nomicfoundation/hardhat-toolbox": "^4.0.0",
    "@openzeppelin/contracts": "^5.0.0",
    "dotenv": "^16.3.0"
  }
}
```

### Step 2 — Configure Environment

Create `.env` in `londyn-legacy-protocol/`:
```
SCROLL_SEPOLIA_RPC=https://sepolia-rpc.scroll.io/
PRIVATE_KEY=<your_deployer_wallet_private_key>
SCROLLSCAN_API_KEY=<your_scrollscan_api_key>
```

Files to edit:
- `hardhat.config.js` — verify Scroll Sepolia network block is present:
```javascript
networks: {
  scrollSepolia: {
    url: process.env.SCROLL_SEPOLIA_RPC,
    accounts: [process.env.PRIVATE_KEY],
    chainId: 534351,
  },
}
```

### Step 3 — Compile Contracts

```bash
npx hardhat compile
```

Files compiled:
- `contracts/ArtistRoyaltyVault.sol`
- `contracts/ScrollVerseToken.sol`
- `contracts/ZakatAutomation.sol`
- `contracts/GuardianRegistry.sol`
- `contracts/RoseGoldEncryption.sol`
- `contracts/RoseGoldEncryptionEnhanced.sol`

**Success criteria:** Zero compilation errors. `artifacts/` directory populated.

### Step 4 — Run Tests

```bash
npx hardhat test
```

Test file: `test/contracts.test.js`

Expected output:
```
ArtistRoyaltyVault
  ✓ deploys with correct split percentages
  ✓ deposits royalties and emits RoyaltyDeposited event
  ✓ distributes artist share correctly (85%)
  ✓ calculates Zakat at 7.77%

ScrollVerseToken
  ✓ has correct name and symbol (ScrollVerse Token / SVT)
  ✓ mints initial supply of 144,000,000 SVT
  ✓ owner can mint additional tokens
  ✓ owner can burn tokens

ZakatAutomation
  ✓ sets Zakat rate to 777 basis points
  ✓ adds recipients with share percentages
  ✓ distributes ETH balance proportionally

GuardianRegistry
  ✓ registers guardian with valid tier
  ✓ rejects invalid tier
  ✓ getGuardianCount returns correct value
  ✓ isGuardian returns true for registered address
```

**Success criteria:** All tests pass.

### Step 5 — Deploy to Scroll Sepolia

```bash
npx hardhat run scripts/deploy.js --network scrollSepolia
```

Deployment script (`scripts/deploy.js`) deploys in this order:
1. `ScrollVerseToken` — stores address as `SVT_ADDRESS`
2. `ZakatAutomation` — stores address as `ZAKAT_ADDRESS`
3. `ArtistRoyaltyVault(SVT_ADDRESS, ZAKAT_ADDRESS)` — stores as `VAULT_ADDRESS`
4. `GuardianRegistry` — stores address as `GUARDIAN_ADDRESS`

Expected console output:
```
ScrollVerseToken deployed to: 0x...
ZakatAutomation deployed to: 0x...
ArtistRoyaltyVault deployed to: 0x...
GuardianRegistry deployed to: 0x...
Deployment complete. Update your .env files with these addresses.
```

### Step 6 — Record Contract Addresses

Update `.env` files in **all repositories** with the deployed addresses:
```
SVT_TOKEN_ADDRESS=0x...
ZAKAT_AUTOMATION_ADDRESS=0x...
ARTIST_ROYALTY_VAULT_ADDRESS=0x...
GUARDIAN_REGISTRY_ADDRESS=0x...
```

Files to update:
- `londyn-legacy-protocol/.env`
- `scrollsoul-music-sync/.env`
- `nextjs-boilerplate/.env.local`

### Step 7 — Verify on Scrollscan

```bash
npx hardhat verify --network scrollSepolia <SVT_ADDRESS>
npx hardhat verify --network scrollSepolia <ZAKAT_ADDRESS>
npx hardhat verify --network scrollSepolia <VAULT_ADDRESS> <SVT_ADDRESS> <ZAKAT_ADDRESS>
npx hardhat verify --network scrollSepolia <GUARDIAN_ADDRESS>
```

Verification URL: `https://sepolia.scrollscan.com/address/<CONTRACT_ADDRESS>#code`

### Phase 1 Success Criteria
- [ ] `npx hardhat compile` — zero errors
- [ ] `npx hardhat test` — all tests pass
- [ ] All 4 contracts deployed to Scroll Sepolia
- [ ] All 4 contract addresses recorded in `.env` files
- [ ] All contracts source-verified on Scrollscan

---

## Phase 2 — Backend Engine (Weeks 3–4)

### Objective
Deploy the `scrollsoul-music-sync` TypeScript backend and connect it to the deployed contracts via ethers.js.

### Repository
`chaishillomnitech1/scrollsoul-music-sync`

### Step 1 — Install Dependencies

```bash
cd scrollsoul-music-sync
npm install
```

Additional packages needed:
```bash
npm install ethers@6 @openzeppelin/contracts
```

### Step 2 — Configure Environment

Create `.env` in `scrollsoul-music-sync/`:
```
PORT=3001
SCROLL_SEPOLIA_RPC=https://sepolia-rpc.scroll.io/
PRIVATE_KEY=<backend_wallet_private_key>
SVT_TOKEN_ADDRESS=0x...
ZAKAT_AUTOMATION_ADDRESS=0x...
ARTIST_ROYALTY_VAULT_ADDRESS=0x...
GUARDIAN_REGISTRY_ADDRESS=0x...
DATABASE_URL=<postgres_or_mongo_connection_string>
JWT_SECRET=<random_32_byte_hex>
```

### Step 3 — Implement Royalty Service

File: `src/services/royaltyService.ts`

```typescript
export interface RoyaltySplit {
  artistShare: number;   // 85% of total
  zakatShare: number;    // 7.77% of total
  daoShare: number;      // 7.23% of total
}

export function calculateRoyaltySplit(totalAmount: number): RoyaltySplit {
  return {
    artistShare: totalAmount * 0.85,
    zakatShare:  totalAmount * 0.0777,
    daoShare:    totalAmount * 0.0723,
  };
}
```

### Step 4 — Implement Guardian Service

File: `src/services/guardianService.ts`

Connects to deployed `GuardianRegistry.sol` via ethers.js:
```typescript
import { ethers } from 'ethers';
// Uses GUARDIAN_REGISTRY_ADDRESS from .env
// Calls: registry.registerGuardian(address, tier)
// Calls: registry.getGuardianCount()
// Calls: registry.isGuardian(address)
```

### Step 5 — Wire API Routes

Files to verify/edit:
- `src/api/routes/artists.ts` — `POST /artists/register`, `GET /artists/:id`, `GET /artists`
- `src/api/routes/royalties.ts` — `POST /royalties/calculate`, `POST /royalties/distribute`, `GET /royalties/history`

### Step 6 — Build and Start Server

```bash
npm run build
npm start
```

Or with Docker:
```bash
docker-compose up --build
```

### Step 7 — Test API Endpoints

```bash
# Health check
curl http://localhost:3001/health

# Calculate royalty split
curl -X POST http://localhost:3001/royalties/calculate \
  -H "Content-Type: application/json" \
  -d '{"totalAmount": 1000}'

# Expected response:
# { "artistShare": 850, "zakatShare": 77.7, "daoShare": 72.3 }

# Register artist
curl -X POST http://localhost:3001/artists/register \
  -H "Content-Type: application/json" \
  -d '{"name": "Test Artist", "walletAddress": "0x...", "genre": "Hip-Hop"}'
```

### Step 8 — Deploy to Railway

1. Install Railway CLI: `npm install -g @railway/cli`
2. Login: `railway login`
3. Initialize: `railway init`
4. Deploy: `railway up`
5. Set environment variables in Railway dashboard
6. Note the public URL: `https://scrollsoul-music-sync.up.railway.app`

### Step 9 — Update Frontend Config

Update `nextjs-boilerplate/.env.local`:
```
NEXT_PUBLIC_BACKEND_URL=https://scrollsoul-music-sync.up.railway.app
```

### Phase 2 Success Criteria
- [ ] `npm run build` — zero TypeScript errors
- [ ] `npm test` — all Jest tests pass
- [ ] `POST /royalties/calculate` returns correct 85/7.77/7.23 split
- [ ] `POST /artists/register` writes to database and returns artist ID
- [ ] Backend deployed and accessible via public URL
- [ ] Royalty distribution triggers on-chain transaction to `ArtistRoyaltyVault.sol`

---

## Phase 3 — Frontend Portal (Weeks 5–6)

### Objective
Deploy the Next.js frontend to Vercel and connect it to the backend API and smart contracts.

### Repository
`chaishillomnitech1/nextjs-boilerplate`

### Step 1 — Install Dependencies

```bash
cd nextjs-boilerplate
npm install
```

Wallet connect packages:
```bash
npm install wagmi viem @wagmi/connectors
```

### Step 2 — Configure Environment

Create `.env.local`:
```
NEXT_PUBLIC_BACKEND_URL=https://scrollsoul-music-sync.up.railway.app
NEXT_PUBLIC_CHAIN_ID=534351
NEXT_PUBLIC_SVT_TOKEN_ADDRESS=0x...
NEXT_PUBLIC_GUARDIAN_REGISTRY_ADDRESS=0x...
NEXT_PUBLIC_ARTIST_ROYALTY_VAULT_ADDRESS=0x...
NEXT_PUBLIC_ZAKAT_AUTOMATION_ADDRESS=0x...
NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID=<from_cloud.walletconnect.com>
```

### Step 3 — Verify Core Pages

Pages that must be present and functional:
- `app/page.tsx` — ScrollVerse homepage with hero, stats bar, navigation
- `app/music/page.tsx` — Featured tracks, royalty display
- `app/artists/page.tsx` — Artist registration form
- `app/dao/page.tsx` — DAO proposals and guardian tiers
- `app/zakat/page.tsx` — Zakat calculator and distribution history

Platform constants (`lib/constants.ts`):
```typescript
export const PLATFORM_CONSTANTS = {
  ARTIST_SHARE: 85,
  ZAKAT_RATE: 7.77,
  DAO_TREASURY: 7.23,
  SVT_TOTAL_SUPPLY: 144_000_000,
  GUARDIAN_TARGET: 144_000,
  ZAKAT_BASIS_POINTS: 777,
}
```

### Step 4 — Add Wallet Connect

Edit `app/layout.tsx` to wrap the app in a wagmi provider configured for Scroll Sepolia:

```typescript
import { WagmiProvider, createConfig, http } from 'wagmi';
import { scrollSepolia } from 'wagmi/chains';
import { injected, walletConnect } from '@wagmi/connectors';

const config = createConfig({
  chains: [scrollSepolia],
  connectors: [
    injected(),
    walletConnect({ projectId: process.env.NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID! }),
  ],
  transports: {
    [scrollSepolia.id]: http(),
  },
});
```

### Step 5 — Connect Frontend to Backend

In `app/api/guardians/route.ts`, proxy to the Railway backend:
```typescript
const BACKEND_URL = process.env.BACKEND_URL || 'http://localhost:3001';

export async function GET() {
  const res = await fetch(`${BACKEND_URL}/guardians`);
  const data = await res.json();
  return Response.json(data);
}
```

### Step 6 — Run Development Server

```bash
npm run dev
```

Open http://localhost:3000 and verify each route loads without errors.

### Step 7 — Deploy to Vercel

```bash
npm install -g vercel
vercel --prod
```

Or connect the GitHub repo to Vercel at https://vercel.com/new and enable automatic deployments from `main`.

Set all `NEXT_PUBLIC_*` environment variables in Vercel dashboard under Settings > Environment Variables.

### Step 8 — End-to-End Flow Test

```
1. Open https://scrollverse.vercel.app
2. Click "Connect Wallet" — MetaMask opens on Scroll Sepolia
3. Go to /artists → fill registration form → submit
   Expected: artist stored in backend DB, wallet recorded on-chain
4. Go to /music → see featured tracks
5. Go to /zakat → enter 1000 → see 77.70 displayed as Zakat amount
6. Go to /dao → see active proposals
```

### Phase 3 Success Criteria
- [ ] `npm run build` — zero errors
- [ ] All 5 pages render without console errors
- [ ] Wallet connect works on Scroll Sepolia
- [ ] Artist registration form submits successfully
- [ ] Zakat calculator shows correct 7.77% of any input
- [ ] Deployed to Vercel with green build status

---

## Phase 4 — Guardian System (Weeks 7–8)

### Objective
Launch the Guardian registration system, connect it to `GuardianRegistry.sol`, and activate the first 1,000 guardians.

### Step 1 — Enable On-Chain Guardian Registration

In `app/dao/page.tsx`, add a "Register as Guardian" section that:
1. Reads wallet address from connected wallet
2. Allows user to select tier (Seeker / Protector / Warrior / Champion / Archangel)
3. Calls `GuardianRegistry.registerGuardian(address, tier)` via wagmi `useWriteContract`

```typescript
import { useWriteContract } from 'wagmi';
import { GUARDIAN_REGISTRY_ABI } from '@/lib/abis';

const { writeContract } = useWriteContract();

function registerAsGuardian(tier: string) {
  writeContract({
    address: process.env.NEXT_PUBLIC_GUARDIAN_REGISTRY_ADDRESS as `0x${string}`,
    abi: GUARDIAN_REGISTRY_ABI,
    functionName: 'registerGuardian',
    args: [walletAddress, tier],
  });
}
```

### Step 2 — Display Live Guardian Count

In `app/page.tsx` stats bar, fetch live count from contract:
```typescript
import { useReadContract } from 'wagmi';

const { data: guardianCount } = useReadContract({
  address: GUARDIAN_REGISTRY_ADDRESS,
  abi: GUARDIAN_REGISTRY_ABI,
  functionName: 'getGuardianCount',
});
```

### Step 3 — Guardian Tier Benefits Page

Create `app/guardians/page.tsx` with:
- Tier breakdown table (Seeker → Archangel)
- Benefits per tier
- Current count per tier (from contract)
- Registration CTA

### Step 4 — Backend Guardian Events Listener

In `scrollsoul-music-sync`, create `src/services/blockchainListener.ts`:
```typescript
import { ethers } from 'ethers';
// Subscribe to GuardianRegistered events
// On each event, store guardian in database
// Update cached guardian count
```

### Step 5 — First 1,000 Guardians Campaign

Launch via:
1. Post registration link on social media
2. Monitor `GuardianRegistered` events on https://sepolia.scrollscan.com
3. Track progress: `npx hardhat run scripts/checkGuardianCount.js --network scrollSepolia`

```javascript
// scripts/checkGuardianCount.js
const registry = await ethers.getContractAt('GuardianRegistry', GUARDIAN_ADDRESS);
const count = await registry.getGuardianCount();
console.log(`Guardian Count: ${count.toString()} / 144,000`);
```

### Phase 4 Success Criteria
- [ ] Guardian registration works end-to-end: wallet → on-chain tx → database
- [ ] Live guardian count displays on homepage
- [ ] 1,000 guardians registered on Scroll Sepolia
- [ ] `isGuardian(address)` returns `true` for registered wallets
- [ ] Tier distribution visible in dashboard

---

## Phase 5 — DAO + Zakat (Weeks 9–10)

### Objective
Activate `ZakatAutomation.sol` with real recipients, launch DAO voting, and execute the first on-chain Zakat distribution.

### Step 1 — Configure Zakat Recipients

```bash
npx hardhat run scripts/setupZakat.js --network scrollSepolia
```

`scripts/setupZakat.js`:
```javascript
const zakat = await ethers.getContractAt('ZakatAutomation', ZAKAT_ADDRESS);

// Add recipients with share percentages (must total ≤ 100)
await zakat.addRecipient('0x<charity1_address>', 40);  // 40% of Zakat
await zakat.addRecipient('0x<charity2_address>', 30);  // 30% of Zakat
await zakat.addRecipient('0x<artist_fund_address>', 30); // 30% of Zakat

console.log('Zakat recipients configured.');
```

### Step 2 — Fund Zakat Contract

Send ETH to `ZakatAutomation` contract address via:
```bash
# Using hardhat
npx hardhat run scripts/fundZakat.js --network scrollSepolia
```

Or via MetaMask: send ETH directly to the contract address.

### Step 3 — First Zakat Distribution

```bash
npx hardhat run scripts/distributeZakat.js --network scrollSepolia
```

`scripts/distributeZakat.js`:
```javascript
const zakat = await ethers.getContractAt('ZakatAutomation', ZAKAT_ADDRESS);
const tx = await zakat.distributeZakat();
await tx.wait();
console.log('Zakat distributed. Tx hash:', tx.hash);
console.log('Verify at: https://sepolia.scrollscan.com/tx/' + tx.hash);
```

### Step 4 — DAO Voting

In `app/dao/page.tsx`, connect proposal voting to backend:

```typescript
// POST /dao/vote
async function castVote(proposalId: number, support: boolean) {
  const res = await fetch(`${BACKEND_URL}/dao/vote`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ proposalId, support, voter: walletAddress }),
  });
  return res.json();
}
```

Backend records vote in database. Guardian status (from `isGuardian()`) gates voting access.

### Step 5 — Automate Monthly Zakat

Configure a cron job in `scrollsoul-music-sync`:

In `src/services/zakatScheduler.ts`:
```typescript
import cron from 'node-cron';
import { ethers } from 'ethers';

// Runs on the 1st of every month at 00:00 UTC
cron.schedule('0 0 1 * *', async () => {
  const provider = new ethers.JsonRpcProvider(process.env.SCROLL_SEPOLIA_RPC);
  const signer = new ethers.Wallet(process.env.PRIVATE_KEY!, provider);
  const zakat = new ethers.Contract(ZAKAT_ADDRESS, ZAKAT_ABI, signer);
  const tx = await zakat.distributeZakat();
  await tx.wait();
  console.log('Monthly Zakat distributed:', tx.hash);
});
```

### Step 6 — Display Zakat History

In `app/zakat/page.tsx`, query `ZakatDistributed` events from the contract:
```typescript
const filter = zakatContract.filters.ZakatDistributed();
const events = await zakatContract.queryFilter(filter, 0, 'latest');
// Render distribution history table
```

### Phase 5 Success Criteria
- [ ] At least 2 Zakat recipients configured on-chain
- [ ] First Zakat distribution executed and verified on Scrollscan
- [ ] Tx hash recorded in `app/zakat/page.tsx` distribution history
- [ ] DAO voting gates by Guardian status (`isGuardian()` check)
- [ ] Monthly Zakat cron job running in production backend
- [ ] Zakat distribution history visible in frontend

---

## Repository Connections Summary

| Source | Target | Method |
|--------|--------|--------|
| `nextjs-boilerplate` (frontend) | `scrollsoul-music-sync` (backend) | HTTP REST API (`NEXT_PUBLIC_BACKEND_URL`) |
| `scrollsoul-music-sync` (backend) | `londyn-legacy-protocol` (contracts) | ethers.js v6, contract addresses in `.env` |
| `nextjs-boilerplate` (frontend) | `londyn-legacy-protocol` (contracts) | wagmi + viem, `NEXT_PUBLIC_*` contract addresses |
| `scrollverse-first-knight-covenant` | All repos | Documentation, ABI snapshots, deployment records |

---

## Environment Variables Checklist

### `londyn-legacy-protocol/.env`
```
SCROLL_SEPOLIA_RPC=
PRIVATE_KEY=
SCROLLSCAN_API_KEY=
SVT_TOKEN_ADDRESS=
ZAKAT_AUTOMATION_ADDRESS=
ARTIST_ROYALTY_VAULT_ADDRESS=
GUARDIAN_REGISTRY_ADDRESS=
```

### `scrollsoul-music-sync/.env`
```
PORT=3001
SCROLL_SEPOLIA_RPC=
PRIVATE_KEY=
SVT_TOKEN_ADDRESS=
ZAKAT_AUTOMATION_ADDRESS=
ARTIST_ROYALTY_VAULT_ADDRESS=
GUARDIAN_REGISTRY_ADDRESS=
DATABASE_URL=
JWT_SECRET=
```

### `nextjs-boilerplate/.env.local`
```
NEXT_PUBLIC_BACKEND_URL=
NEXT_PUBLIC_CHAIN_ID=534351
NEXT_PUBLIC_SVT_TOKEN_ADDRESS=
NEXT_PUBLIC_GUARDIAN_REGISTRY_ADDRESS=
NEXT_PUBLIC_ARTIST_ROYALTY_VAULT_ADDRESS=
NEXT_PUBLIC_ZAKAT_AUTOMATION_ADDRESS=
NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID=
```

---

## Key External Resources

| Resource | URL |
|----------|-----|
| Scroll Sepolia Faucet | https://sepolia-faucet.scroll.io/ |
| Scroll Sepolia Explorer | https://sepolia.scrollscan.com |
| Scroll Sepolia RPC | https://sepolia-rpc.scroll.io/ |
| OpenZeppelin Contracts | https://docs.openzeppelin.com/contracts |
| Hardhat Docs | https://hardhat.org/docs |
| wagmi Docs | https://wagmi.sh |
| Railway Deploy | https://railway.app |
| Vercel Deploy | https://vercel.com |
| WalletConnect Cloud | https://cloud.walletconnect.com |

---

## Milestone Tracker

| Milestone | Target Date | Status |
|-----------|-------------|--------|
| All contracts compiled | Week 1 | ⏳ |
| All contracts deployed to Scroll Sepolia | Week 2 | ⏳ |
| Backend deployed to Railway | Week 3 | ⏳ |
| Backend ↔ contracts integration live | Week 4 | ⏳ |
| Frontend deployed to Vercel | Week 5 | ⏳ |
| Full end-to-end flow tested | Week 6 | ⏳ |
| Guardian registration live | Week 7 | ⏳ |
| 1,000 guardians registered | Week 8 | ⏳ |
| First Zakat distributed on-chain | Week 9 | ⏳ |
| DAO voting live with guardian gate | Week 10 | ⏳ |

---

**بِسْمِ اللَّهِ — The architecture is clear. The path is straight. Build. 🕋⚡️**
