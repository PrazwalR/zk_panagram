# **ZK Panagram Game**

This is an on-chain ZK panagram game that uses Noir for the ZK circuits and Foundry for the smart contracts. Players must prove they know a valid anagram of a given word without revealing the solution publicly.

## **Setup**

### **1. Clone the repo**

```bash
git clone https://github.com/PrazwalR/zk_panagram.git
cd zk_panagram
```

### **2. Organize project structure**
Move the following files into a `contracts` folder:
- `js-scripts/`
- `src/`
- `test/`
- `foundry.lock`
- `foundry.toml`

```bash
mkdir contracts
mv js-scripts src test foundry.lock foundry.toml contracts/
```

### **3. Install dependencies**

```bash
npm install && cd contracts && forge install
```

## **Testing**

### **Running the tests**

```bash
cd contracts
forge test
```

## **Circuit Development**

### **(Optional) Re-creating the verifier**
This step is needed if you modify the circuit logic.

1. Navigate inside the circuits folder and compile the circuit:

```bash
cd circuits
nargo compile
```

2. Generate the verification key:

```bash
bb write_vk --oracle_hash keccak -b ./target/circuits.json -o ./target
```

3. Generate the verifier:

```bash
bb write_solidity_verifier -k ./target/vk -o ./target/Verifier.sol
```

4. Replace the old `Verifier.sol` in `contracts/src` with the newly generated one.

## **Deployment**

### **1. Deploy the Verifier Contract**

```bash
cd contracts
forge create src/Verifier.sol:UltraHonkVerifier --account <your-account> --rpc-url <your-rpc-url> --broadcast
```

### **2. Deploy the Panagram Contract**

```bash
forge create src/Panagram.sol:Panagram --account <your-account> --rpc-url <your-rpc-url> --broadcast --constructor-args <your-verifier-address>
```

### **3. Setup a game round**

Generate the word hash for your chosen word (replace `"triangles"` with your desired word):

```bash
chisel
```

Then in chisel:
```solidity
bytes32(uint256(keccak256("triangles")) % 21888242871839275222246405745257275088548364400416034343698204186575808495617)
```

Take the output and create a new round:

```bash
cast send <your-panagram-address> "newRound(bytes32)" <chisel-output>
```

## **How to Play**

1. Players receive a scrambled version of the target word
2. They must find a valid anagram
3. Using the ZK circuit, they can submit a proof that they know the correct anagram without revealing it
4. The smart contract verifies the proof and awards points if valid

## **Frontend Development**

No frontend is currently integrated with this repository. Developers interested in building a user interface can fork this repository and create their own frontend implementation using the deployed smart contracts.

## **Project Structure**

```
zk_panagram/
├── circuits/           # Noir ZK circuits
├── contracts/          # Foundry smart contracts
│   ├── js-scripts/     # JavaScript utilities
│   ├── src/           # Solidity contracts
│   ├── test/          # Contract tests
│   ├── foundry.lock   # Dependency lock file
│   └── foundry.toml   # Foundry configuration
├── package.json       # Node.js dependencies
└── README.md          # This file
```

## **Requirements**

- [Node.js](https://nodejs.org/) (v16 or later)
- [Foundry](https://book.getfoundry.sh/getting-started/installation)
- [Noir](https://noir-lang.org/getting_started/nargo_installation)
- [bb (Barretenberg)](https://github.com/AztecProtocol/barretenberg)

## **Contributing**

Feel free to fork this repository and build upon it. Some potential enhancements:
- Frontend integration
- Multiple difficulty levels
- Multiplayer functionality
- Additional word categories
- Mobile-friendly interface
