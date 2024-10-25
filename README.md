<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Token Purchase</title>
    <style>
        /* CSS for basic styling */
        body { font-family: Arial, sans-serif; }
        #walletAddress, #tokenBalance { margin: 10px 0; }
        button { padding: 10px; margin-top: 10px; }
    </style>
</head>
<body>
    <h1>Fund Share Token Purchase</h1>

    <button id="connectWallet">Connect Wallet</button>
    <div id="walletAddress">Wallet Address: Not connected</div>

    <button id="checkWhitelist">Check Whitelist Status</button>
    <div id="whitelistStatus">Whitelist Status: Unknown</div>

    <button id="buyTokens" disabled>Buy Tokens</button>
    <div id="purchaseStatus">Purchase Status: Not initiated</div>

    <!-- Include Ethers.js for blockchain interaction -->
    <script src="https://cdn.jsdelivr.net/npm/ethers@5.0.0/dist/ethers.min.js"></script>
    
    <script>
        let provider;
        let contract;
        const contractAddress = "YOUR_CONTRACT_ADDRESS"; // Replace with your contract's address
        const contractABI = [
            // Paste ABI array here from your contract
        ];

        // Connect Wallet function
        async function connectWallet() {
            provider = new ethers.providers.Web3Provider(window.ethereum);
            await provider.send("eth_requestAccounts", []);
            const signer = provider.getSigner();
            document.getElementById("walletAddress").innerText = "Wallet Address: " + await signer.getAddress();
            contract = new ethers.Contract(contractAddress, contractABI, signer);
        }

        // Check Whitelist Status
        async function checkWhitelistStatus() {
            const signer = provider.getSigner();
            const userAddress = await signer.getAddress();
            const isWhitelisted = await contract.whitelisted(userAddress);
            document.getElementById("whitelistStatus").innerText = "Whitelist Status: " + (isWhitelisted ? "Approved" : "Not approved");
            document.getElementById("buyTokens").disabled = !isWhitelisted;
        }

        // Buy Tokens function
        async function buyTokens() {
            const tx = await contract.purchaseTokens({
                value: ethers.utils.parseEther("0.1") // Set desired ETH/MATIC value per purchase
            });
            await tx.wait();
            document.getElementById("purchaseStatus").innerText = "Purchase Status: Successful!";
        }

        document.getElementById("connectWallet").onclick = connectWallet;
        document.getElementById("checkWhitelist").onclick = checkWhitelistStatus;
        document.getElementById("buyTokens").onclick = buyTokens;
    </script>
</body>
</html>
