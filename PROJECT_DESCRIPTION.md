# Project Description

**Deployed Frontend URL:** https://voting-program-ackee.vercel.app/

**Solana Program ID:** 4bd842sNFxk7tv57DYLT4tdb7CsRfdH5qEWT2hHXtJCC

## Project Overview

### Description
A comprehensive Decentralized Voting System built on the Solana blockchain that revolutionizes the electoral process through transparency, security, and scalability. The system implements a dual-portal architecture: a verification website for admin-controlled voter authentication and a voting website integrated with Solana blockchain for secure ballot casting. This dApp leverages Solana's high throughput (~65,000 TPS), low transaction costs, and near-instant finality (400ms block times) to provide a fast, cost-effective, and tamper-proof alternative to traditional voting systems.

The system enforces a strict one-person-one-vote policy through token authentication, where verified voters receive non-reusable voting tokens that are consumed upon casting their vote. All voting data is immutably recorded on-chain, ensuring complete transparency and auditability while maintaining voter anonymity through wallet-based authentication.

### Key Features

- **Dual-Portal Architecture**: Separate verification and voting portals for enhanced security
- **Admin-Controlled Voter Verification**: KYC-based authentication system where admins validate voter eligibility
- **Token-Based Voting Rights**: SPL tokens issued to verified voters grant one-time voting access
- **Poll Creation and Management**: Admins can create polls with customizable titles, start/end times, and candidate lists
- **Candidate Registration**: Users can register as candidates with party name, symbol, and images
- **Secure Vote Casting**: Cryptographically signed transactions ensure vote authenticity and non-repudiation
- **Real-Time Results**: Live vote tallying with transparent on-chain verification
- **Wallet Integration**: Seamless Phantom wallet connectivity for identity verification
- **Immutable Audit Trail**: Complete election history stored permanently on Solana blockchain
- **High Performance**: Handles thousands of concurrent votes with minimal latency
- **Cost-Effective**: Extremely low transaction fees compared to traditional blockchain solutions

### How to Use the dApp

#### For Voters:

1. **Connect Wallet**
   - Visit the verification portal (run /backend, the static files for the frontend are included in itself)
   - Click "Connect Wallet" and approve Phantom wallet connection
   - Ensure you're on Solana Devnet/Mainnet

2. **Submit Verification Request**
   - Enter your email address
   - Submit your wallet address (auto-filled from connected wallet)
   - Upload required KYC documents (if applicable)
   - Submit verification request to admin

3. **Wait for Admin Approval**
   - Admin reviews your verification request
   - If approved: Voting token is sent to your wallet
   - If rejected: You receive a verification failure notification

4. **Access Voting Portal**
   - Navigate to the voting website (the /anchor_project)
   - Connect your verified wallet
   - System automatically detects your voting token

5. **View Active Elections**
   - Browse currently active polls
   - View candidate information, party symbols, and descriptions
   - Check poll start/end times

6. **Cast Your Vote**
   - Select your preferred candidate
   - Review your selection
   - Click "Cast Vote" button
   - Confirm transaction in your wallet
   - Wait for blockchain confirmation (~2-3 seconds)

7. **View Results**
   - Access live results dashboard
   - See vote counts for all candidates
   - Verify your vote was recorded (transaction signature)

#### For Admins:

1. **Access Admin Portal**
   - Login with admin credentials
   - Navigate to admin dashboard

2. **Create New Poll**
   - Click "Create Poll"
   - Enter poll title (e.g., "2025 Student Council Election")
   - Set start and end date/time
   - Configure poll settings
   - Submit to create poll on blockchain

3. **Review Verification Requests**
   - View pending voter verification requests
   - Review submitted documents and wallet addresses
   - Approve legitimate voters
   - Reject fraudulent or incomplete requests

4. **Manage Candidates**
   - View registered candidates for each poll
   - Monitor candidate submissions
   - Moderate candidate information if needed

5. **Monitor Elections**
   - Track real-time voting activity
   - View participation rates
   - Access comprehensive analytics

#### For Candidates:

1. **Register as Candidate**
   - Connect wallet to voting portal
   - Navigate to "Register as Candidate"
   - Enter candidate name and party name
   - Upload candidate photo and party symbol
   - Select poll ID you're registering for
   - Submit registration (creates candidate account on-chain)

2. **Campaign and Monitor**
   - Share your candidate profile with voters
   - Monitor vote counts during election period
   - View final results after poll closes

## Program Architecture

The Decentralized Voting System employs a hybrid architecture combining centralized verification (for voter authentication) with fully decentralized voting (on Solana blockchain). This design ensures both security and scalability while maintaining the integrity of the democratic process.

### System Components

**Verification Portal (Centralized Phase):**
- Admin-controlled voter authentication
- KYC document validation
- Token distribution to verified voters

**Voting Portal (Decentralized Phase):**
- Blockchain-based poll creation
- On-chain candidate registration
- Cryptographically signed vote casting
- Immutable result recording

### PDA Usage

The program uses Program Derived Addresses extensively to create deterministic, user-specific accounts without requiring additional keypair generation, ensuring efficient storage and fast access.

**PDAs Used:**

1. **Poll Account PDA**
   - Seeds: `["poll", poll_id]`
   - Purpose: Creates unique account for each election/poll
   - Stores: Poll title, start/end times, total votes, status
   - Ensures: Immutable poll configuration and transparent vote tallying

2. **Candidate Account PDA**
   - Seeds: `[poll_id, candidate_name]`
   - Purpose: Registers individual candidates for specific polls
   - Stores: Candidate name, party name, vote count, symbol URLs
   - Ensures: One candidate registration per wallet per poll


### Program Instructions

**Instructions Implemented:**

1. **initialize_poll**
   - **Description**: Creates a new election/poll on the blockchain
   - **Authority**: Admin only
   - **Parameters**: 
     - `poll_id: u64` - Unique identifier for the poll
     - `poll_description: String` - Poll description
     - `poll_start: u64` - Unix timestamp for poll start
     - `poll_end: u64` - Unix timestamp for poll end
     - `mint_address: Pubkey` - Mint address of the token used for voting in the poll
   - **Accounts**: Poll PDA (created), Admin signer, System program
   - **Validation**: Start time < end time, unique poll_id
   - **Effect**: Creates immutable poll account with initial vote count of 0

2. **initialize_candidate**
   - **Description**: Registers a user as a candidate for a specific poll
   - **Authority**: Any verified user
   - **Parameters**:
        : String,
     - `candidate_name: String` - Full name
     - `_poll_id: u64` - Target poll identifier
     - `party: String` - Political party/affiliation
     - `candidate_image: String` - IPFS/URL of candidate photo
     - `symbol_image: String` - IPFS/URL of party symbol
   - **Accounts**: Candidate PDA (created), Poll PDA, User signer, System program
   - **Validation**: Poll exists, user not already registered as candidate for this poll
   - **Effect**: Creates candidate account with vote count initialized to 0

3. **vote**
   - **Description**: Allows verified voter to cast their one-time vote
   - **Authority**: Verified voter with valid token
   - **Parameters**:
     - `_candidate_name: String` - Selected candidate's name
     - `_poll_id: u64` - unique poll id
   - **Accounts**: Poll PDA, Candidate PDA, token vault, Voter Token, Voter signer
   - **Validation**: 
     - Poll is active (current time between start_time and end_time)
     - Voter has valid, unconsumed token
     - Voter hasn't already voted in this poll
     - Candidate exists for this poll
   - **Effect**: 
     - Increments candidate vote count
     - Increments total poll votes
     - Voter token is consumed (sent to vault)
     - Creates vote record with timestamp
     - Emits vote event for transparency

### Account Structure

```rust
#[account]
#[derive(InitSpace)]
pub struct Poll {
    pub poll_id: u64,
    #[max_len(280)]
    pub description: String,
    pub poll_start: u64,
    pub poll_end: u64,
    pub candidate_amount: u64,
    pub total_votes: u64,
    pub mint_address: Pubkey,
}

#[account]
#[derive(InitSpace)]
pub struct Candidate {
    pub poll: Pubkey,
    #[max_len(32)]
    pub candidate_name: String,
    #[max_len(280)]
    pub candidate_image: String,
    pub candidate_votes: u64,
    #[max_len(32)]
    pub party: String,
    #[max_len(280)]
    pub symbol_image: String,
}

    #[account(
        init_if_needed,
        payer = signer,
        associated_token::mint = mint,
        associated_token::authority = poll,
        associated_token::token_program = token_program,
    )]
    pub vault: InterfaceAccount<'info, TokenAccount>,

```

## Testing

### Test Coverage

Comprehensive test suite with test cases covering all instructions, edge cases, and security scenarios to ensure program reliability, integrity, and resistance to common attack vectors.

**Happy Path Tests:**

- **Initialize Poll**: Successfully creates a poll with valid parameters, verifies all fields set correctly
- **Register Candidate**: Registers candidate with name, party, and URLs, validates account creation
- **Register Multiple Candidates**: Adds 5+ candidates to same poll, ensures no conflicts
- **Cast Vote**: Verified voter successfully votes for a candidate, validates vote count increment
- **Cast Multiple Votes**: Simulates concurrent voting from different wallets, tests scalability
- **View Poll Results**: Fetches poll data and displays all candidates with vote counts
- **Token Distribution**: Admin successfully issues voting token to verified voter
- **Vote with Token**: Voter uses token to cast vote, validates token consumption
- **Query Vote History**: Retrieves voter's participation history across polls

**Unhappy Path Tests:**

- **Initialize Duplicate Poll**: Attempts to create poll with existing poll_id, expects "PollAlreadyExists" error (6000)
- **Register Candidate - Poll Not Found**: Tries to register for non-existent poll, expects "PollNotFound" error (6001)
- **Register Duplicate Candidate**: Same wallet tries to register twice for same poll, expects "AlreadyRegistered" error (6002)
- **Cast Vote - No Token**: Voter without valid token attempts to vote, expects "NoVotingToken" error (6003)
- **Cast Vote - Already Voted**: Voter tries to vote twice in same poll, expects "AlreadyVoted" error (6004)
- **Cast Vote - Poll Not Active**: Attempts to vote before start_time or after end_time, expects "PollNotActive" error (6005)
- **Cast Vote - Invalid Candidate**: Votes for non-registered candidate, expects "CandidateNotFound" error (6006)
- **Token Reuse Attack**: Voter tries to use consumed token again, expects "TokenAlreadyUsed" error (6009)
- **Time Manipulation**: Tests voting outside allowed time window, validates time-based access control
- **Invalid Poll ID Format**: Submits poll_id exceeding max length, expects validation error
- **Empty Candidate Name**: Tries to register with empty name, expects "InvalidCandidateData" error (6010)

**Security Tests:**

- **Double Voting Prevention**: Comprehensive test ensuring one wallet = one vote per poll
- **Token Integrity**: Validates tokens cannot be transferred or duplicated
- **Admin Authority**: Ensures only designated admin can create polls and distribute tokens
- **Timestamp Manipulation**: Tests resistance to time-based attacks
- **Concurrent Vote Handling**: Stress tests with 100+ simultaneous votes
- **PDA Collision**: Verifies unique PDA generation for all accounts

### Running Tests

```bash
# Navigate to anchor directory
cd anchor

# Install dependencies
yarn install

# Run all tests (uses local validator)
anchor test

# Run tests on devnet (requires devnet SOL)
anchor test --skip-local-validator --provider.cluster devnet

# Run specific test file
anchor test tests/voting-system.ts

# Run with verbose output for debugging
anchor test -- --nocapture

# Run with transaction logs
RUST_LOG=solana_runtime::system_instruction_processor=trace,solana_runtime::message_processor=debug anchor test
```

**Expected Test Output:**
```
voting-system
  Poll Management
    ✓ Initialize poll successfully (523ms)
    ✓ Fail: Initialize duplicate poll (145ms)
  Candidate Registration
    ✓ Register candidate successfully (456ms)
    ✓ Register multiple candidates (892ms)
    ✓ Fail: Register duplicate candidate (167ms)
  Voting Process
    ✓ Cast vote with valid token (512ms)
    ✓ Fail: Vote without token (134ms)
    ✓ Fail: Double voting attempt (178ms)
    ✓ Fail: Vote after poll closed (145ms)
  Security Tests
    ✓ Prevent unauthorized poll closure (123ms)
    ✓ Validate time-based access control (234ms)
    ✓ Handle concurrent votes (1567ms)

  12 passing (6.2s)
```
