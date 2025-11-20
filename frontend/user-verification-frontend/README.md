# Decentralized Voting System - Frontend (Voting Portal)

Modern, responsive Next.js application for the voting interface of the Decentralized Voting System. This portal allows verified voters to cast their votes securely on the Solana blockchain.

## Prerequisites

- **Node.js**: 18.18.0 or higher
- **pnpm/yarn**: Latest version
- **Phantom Wallet** (or compatible Solana wallet)
- **Solana Devnet/Mainnet** access

## Quick Start

```bash
# Install dependencies
pnpm install

# Start development server
pnpm dev

# Build for production
pnpm build

# Start production server
pnpm start

# Preview production build
pnpm preview
```

The app will be available at `http://localhost:3000`

## 🛠️ Tech Stack

- **Framework**: Next.js 14.x (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS 3.x
- **Blockchain**: 
  - @solana/web3.js ^1.95.0
  - @solana/wallet-adapter-react ^0.15.35
  - @coral-xyz/anchor ^0.30.0
- **State Management**: React Context + Hooks
- **UI Components**: Custom components with Tailwind
- **Notifications**: react-hot-toast
- **Form Handling**: React Hook Form (optional)
- **HTTP Client**: Axios / fetch

## Project Structure

```
frontend/
├── src/
│   ├── app/
│   │   ├── layout.tsx              # Root layout with wallet provider
│   │   ├── page.tsx                # Home/landing page
│   │   ├── polls/
│   │   │   ├── page.tsx           # Browse active polls
│   │   │   └── [pollId]/
│   │   │       ├── page.tsx       # Vote casting page
│   │   │       └── results/
│   │   │           └── page.tsx   # Results page
│   │   ├── register-candidate/
│   │   │   └── page.tsx           # Candidate registration
│   │   └── profile/
│   │       └── page.tsx           # User profile & vote history
│   ├── components/
│   │   ├── wallet/
│   │   │   ├── WalletProvider.tsx # Solana wallet configuration
│   │   │   └── WalletButton.tsx   # Connect wallet button
│   │   ├── polls/
│   │   │   ├── PollCard.tsx       # Poll display card
│   │   │   ├── PollList.tsx       # List of polls
│   │   │   └── PollFilter.tsx     # Filter polls
│   │   ├── voting/
│   │   │   ├── CandidateCard.tsx  # Candidate display
│   │   │   ├── VoteButton.tsx     # Vote action button
│   │   │   └── VoteConfirm.tsx    # Confirmation modal
│   │   ├── results/
│   │   │   ├── ResultsChart.tsx   # Vote count visualization
│   │   │   └── ResultsTable.tsx   # Tabular results
│   │   └── ui/
│   │       ├── Button.tsx         # Reusable button
│   │       ├── Card.tsx           # Card component
│   │       ├── Modal.tsx          # Modal dialog
│   │       └── Loading.tsx        # Loading spinner
│   ├── lib/
│   │   ├── anchor/
│   │   │   ├── idl.json          # Program IDL
│   │   │   ├── setup.ts          # Anchor program setup
│   │   │   └── instructions.ts   # Typed instruction calls
│   │   ├── solana/
│   │   │   ├── connection.ts     # RPC connection setup
│   │   │   └── utils.ts          # Solana utility functions
│   │   └── utils/
│   │       ├── format.ts         # Data formatting helpers
│   │       └── time.ts           # Date/time utilities
│   ├── hooks/
│   │   ├── useProgram.ts         # Anchor program hook
│   │   ├── usePolls.ts           # Fetch polls
│   │   ├── useCandidates.ts      # Fetch candidates
│   │   ├── useVote.ts            # Vote casting logic
│   │   └── useToken.ts           # Token status check
│   ├── contexts/
│   │   └── ProgramContext.tsx    # Program state context
│   ├── types/
│   │   ├── poll.ts               # Poll types
│   │   ├── candidate.ts          # Candidate types
│   │   └── vote.ts               # Vote types
│   └── styles/
│       └── globals.css           # Global styles
├── public/
│   ├── images/
│   └── icons/
├── .env.local                     # Environment variables
├── next.config.js
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

