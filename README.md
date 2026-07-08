# CrowdChain — Decentralized Crowdfunding dApp
 
A decentralized crowdfunding platform built on Ethereum that enables verified creators to launch fundraising campaigns while ensuring transparency through smart contracts and decentralized storage.
 
## Features
 
- Secure Ethereum smart contracts (Solidity)
- Creator verification using community voting (5 upvotes to verify)
- IPFS integration for decentralized document storage (Pinata)
- ETH-based crowdfunding campaigns with editable title/description
- MetaMask wallet integration
- Automatic contract deployment with Hardhat (address auto-written to frontend)
- Refund mechanism for unsuccessful campaigns
- Withdrawal only after funding goals are met and deadline passed
- Built-in gas/performance analysis script
## Tech Stack
 
- Solidity 0.8.20
- Hardhat
- Ethers.js v6
- JavaScript
- HTML/CSS
- MetaMask
- IPFS (Pinata)
- Node.js
## Setup (one time only)
 
```bash
npm install
```
 
---
 
## Running the project — 3 commands total
 
### Terminal 1 — Start local blockchain (keep this open)
```bash
npm run node
```
 
### Terminal 2 — Deploy contract (compiles + deploys + writes address automatically)
```bash
npm run deploy
```
 
### Terminal 2 — Serve the frontend
```bash
npm start
```
 
Then open **http://localhost:3000** in your browser.
 
That's it. No manual copy-pasting of addresses.
 
---
 
## Deploying to Sepolia testnet instead
 
1. Copy `.env.example` to `.env` and fill in:
```
   PRIVATE_KEY=your_metamask_private_key
   SEPOLIA_RPC_URL=https://eth-sepolia.g.alchemy.com/v2/your_key
```
2. Run:
```bash
   npm run deploy:sepolia
   npm start
```
 
Get free Sepolia ETH at: https://sepoliafaucet.com
Get a free RPC at: https://alchemy.com
 
---
 
## MetaMask setup for localhost
 
Add a custom network in MetaMask:
- **RPC URL**: http://127.0.0.1:8545
- **Chain ID**: 31337
- **Currency**: ETH
Import a test wallet by copying a private key from the `npm run node` output.
 
---
 
## IPFS setup (Pinata)
 
1. Sign up free at https://app.pinata.cloud
2. Create an API key (V1)
3. Set your own key/secret in `frontend/app.js`:
```js
   const PINATA_API_KEY    = "your_key";
   const PINATA_SECRET_KEY = "your_secret";
```
 
⚠️ **Do not commit real Pinata keys.** Treat them as secrets — use environment variables or a gitignored config file, and rotate any key that has been shared or committed.
 
---
 
## Gas & performance analysis
 
`scripts/analyze.js` deploys a fresh instance and runs a full flow (application, voting, campaign creation, contribution, withdrawal) against local test accounts, printing gas usage, ETH cost, and timing per function.
 
```bash
npx hardhat run scripts/analyze.js --network localhost
```
 
(Requires `npm run node` running in another terminal first.)
 
---
 
## Project structure
 
```
crowdchain/
├── contracts/
│   └── Crowdfunding.sol         ← Smart contract
├── scripts/
│   ├── deploy.js                ← Deploy + auto-writes address
│   └── analyze.js               ← Gas/performance report
├── frontend/
│   ├── index.html                ← UI
│   ├── style.css                 ← Styles
│   ├── app.js                    ← All client logic (wallet, IPFS, contract calls)
│   └── contract.js               ← ABI + address (auto-updated on deploy)
├── server.js                     ← Tiny static server (npm start)
├── hardhat.config.js
└── package.json
```
 
---
 
## Smart contract functions
 
| Function | Who | Description |
|---|---|---|
| `applyForVerification(hash)` | Anyone | Submit IPFS proof hash |
| `voteOnCreator(addr, bool)` | Anyone (not self) | Upvote or downvote a creator; auto-verifies at 5 upvotes |
| `createCampaign(title, desc, goal, deadline)` | Verified creators | Create a campaign |
| `editCampaign(id, title, desc)` | Creator | Edit title/description (goal & deadline locked) |
| `contribute(id)` | Anyone | Send ETH to a campaign before its deadline |
| `withdrawFunds(id)` | Creator | Withdraw if goal met, after deadline |
| `refund(id)` | Contributors | Refund if goal not met, after deadline |
 
### Read-only helpers
`getCampaignCount`, `getCampaignDetails`, `getCreatorProof`, `getVotes`, `hasVoted`, `getContribution`, `VOTE_THRESHOLD`
 
---
 
## Screenshots
 
Place image files in `docs/screenshots/` with the filenames below (or update the paths to match yours).
 
### Home Page
![Home Page](docs/screenshots/home-page.png)
 
### Home Page: How It Works
![Home Page - How It Works](docs/screenshots/home-how-it-works.png)
 
### Get Verified Page — Pending Verification Request
![Get Verified - Pending](docs/screenshots/get-verified-pending.png)
 
### Get Verified Page — Verification Complete
![Get Verified - Complete](docs/screenshots/get-verified-complete.png)
 
### Vote on Creators — Landing Page (For Candidate)
![Vote on Creators - Candidate](docs/screenshots/vote-landing-candidate.png)
 
### Vote on Creators Page — For User
![Vote on Creators - User](docs/screenshots/vote-page-user.png)
 
### Vote on Creators Page — For Voter
![Vote on Creators - Voter](docs/screenshots/vote-page-voter.png)
 
### Create a Campaign Page
![Create a Campaign](docs/screenshots/create-campaign.png)
 
---
 
## Performance & Analysis Charts
 
Generated via `npx hardhat run scripts/analyze.js --network localhost`. Place image files in `docs/charts/` with the filenames below (or update the paths to match yours).
 
### Dashboard
![Dashboard](docs/charts/dashboard.png)
 
### Gas Usage
![Gas Usage](docs/charts/gas-usage.png)
 
### Gas Distribution
![Gas Distribution](docs/charts/gas-distribution.png)
 
### ETH Cost
![ETH Cost](docs/charts/eth-cost.png)
 
### View Latency
![View Latency](docs/charts/view-latency.png)
 
### Voting Progression
![Voting Progression](docs/charts/voting-progression.png)
 
### Security Scenarios
![Security Scenarios](docs/charts/security-scenarios.png)
 
---
 
## Security notes
 
- Withdraw/refund follow checks-effects-interactions (state updated before ETH transfer) to prevent reentrancy.
- No admin/owner role — verification is fully community-governed via voting.
- Contract has no pause/emergency-stop mechanism; consider adding one before mainnet use.
- Frontend Pinata keys are client-side and visible to anyone who views source — fine for a local demo, not safe for production as-is.
