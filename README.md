# Function Frontend

This Solidity program is a simple implementation of 2 or 3 functions to the front end and showing the values of those functions.

## Description

This program is a Smart Contract written in Solidity and Java, a programming language used for developing smart contracts on the Ethereum blockchain. The project needs a 2 or 3 function in frontend. This program serves as functions and Errors - ETH + AVAX to advance on the next module. 

Use this as guidance in executing the program and all files you need are here https://github.com/MetacrafterChris/SCM-Starter.
~Credits Sir Chris

## Getting Started

### Executing program

To run this program, you can use VS Code, An application used on many varies. To get started, download VS Code and install on your computer.

Once you've dowloaded the VS Code download the starter pack and open it in your VS Code.


```javascript
import { useState, useEffect } from "react";
import { ethers } from "ethers";
import atm_abi from "../artifacts/contracts/Assessment.sol/Assessment.json";

export default function HomePage() {
  const [ethWallet, setEthWallet] = useState(undefined);
  const [account, setAccount] = useState(undefined);
  const [atm, setATM] = useState(undefined);
  const [balance, setBalance] = useState(undefined);
  const [transactionHistory, setTransactionHistory] = useState([]);
  const [depositAmount, setDepositAmount] = useState('');
  const [withdrawAmount, setWithdrawAmount] = useState('');

  const contractAddress = "0x5FbDB2315678afecb367f032d93F642f64180aa3";
  const atmABI = atm_abi.abi;

  const getWallet = async () => {
    if (window.ethereum) {
      setEthWallet(window.ethereum);
    }

    if (ethWallet) {
      const accounts = await ethWallet.request({ method: "eth_accounts" });
      handleAccount(accounts);
    }
  };

  const handleAccount = (accounts) => {
    if (accounts.length > 0) {
      console.log("Account connected: ", accounts[0]);
      setAccount(accounts[0]);
    } else {
      console.log("No account found");
    }
  };

  const connectAccount = async () => {
    if (!ethWallet) {
      alert("MetaMask wallet is required to connect");
      return;
    }

    const accounts = await ethWallet.request({ method: "eth_requestAccounts" });
    handleAccount(accounts);

    // once wallet is set we can get a reference to our deployed contract
    getATMContract();
  };

  const getATMContract = () => {
    const provider = new ethers.providers.Web3Provider(ethWallet);
    const signer = provider.getSigner();
    const atmContract = new ethers.Contract(contractAddress, atmABI, signer);

    setATM(atmContract);
  };

  const getBalance = async () => {
    if (atm) {
      setBalance((await atm.getBalance()).toNumber());
    }
  };

  const deposit = async (amount) => {
    if (atm && amount > 0) {
      let tx = await atm.deposit(amount);
      await tx.wait();
      getBalance();
      updateTransactionHistory("Deposit", amount);
    }
  };

  const withdraw = async (amount) => {
    if (atm && amount > 0) {
      let tx = await atm.withdraw(amount);
      await tx.wait();
      getBalance();
      updateTransactionHistory("Withdraw", -amount);
    }
  };

  const updateTransactionHistory = (action, amount) => {
    const newTransaction = {
      action: action,
      amount: amount,
      timestamp: new Date().toLocaleString()
    };
    const newTransactionHistory = [...transactionHistory, newTransaction];
    setTransactionHistory(newTransactionHistory);
  };

  const getLastFiveTransactions = () => {
    return transactionHistory.slice(-5).reverse();
  };

  const resetTransactionHistory = () => {
    setTransactionHistory([]);
  };

  const initUser = () => {
    // Check to see if user has Metamask
    if (!ethWallet) {
      return <p>Please install Metamask in order to use this ATM.</p>;
    }

    // Check to see if user is connected. If not, connect to their account
    if (!account) {
      return <button onClick={connectAccount}>Please connect your Metamask wallet</button>;
    }

    if (balance === undefined) {
      getBalance();
    }

    return (
      <div>
        <p>Your Account: {account}</p>
        <p>Your Balance: {balance} ETH</p>
        <div>
          <input
            type="number"
            placeholder="Enter amount to deposit"
            value={depositAmount}
            onChange={(e) => setDepositAmount(e.target.value)}
          />
          <button onClick={() => deposit(Number(depositAmount))}>Deposit ETH</button>
        </div>
        <div>
          <input
            type="number"
            placeholder="Enter amount to withdraw"
            value={withdrawAmount}
            onChange={(e) => setWithdrawAmount(e.target.value)}
          />
          <button onClick={() => withdraw(Number(withdrawAmount))}>Withdraw ETH</button>
        </div>
        <div>
          <h3>Transaction History:</h3>
          <ul>
            {transactionHistory.map((transaction, index) => (
              <li key={index}>
                {transaction.action} {Math.abs(transaction.amount)} ETH - {transaction.timestamp}
              </li>
            ))}
          </ul>
        </div>
        <div>
          <h3>Last 5 Transactions:</h3>
          <ul>
            {getLastFiveTransactions().map((transaction, index) => (
              <li key={index}>
                {transaction.action} {Math.abs(transaction.amount)} ETH - {transaction.timestamp}
              </li>
            ))}
          </ul>
        </div>
        <button onClick={resetTransactionHistory}>Reset Transaction History</button>
      </div>
    );
  };

  useEffect(() => {
    getWallet();
  }, []);

  return (
    <main className="container">
      <header>
        <h1>Welcome to the Metacrafters ATM!</h1>
      </header>
      {initUser()}
      <style jsx>{`
        .container {
          text-align: center;
        }
        input {
          margin: 10px;
          padding: 5px;
          font-size: 1em;
        }
        button {
          margin: 10px;
          padding: 10px 20px;
          font-size: 1em;
          background-color: #4CAF50;
          color: white;
          border: none;
          border-radius: 5px;
          cursor: pointer;
        }
        button:hover {
          background-color: #45a049;
        }
      `}</style>
    </main>
  );
}
  

```

To open the index js, you have to follow the "Starter Next/Hardhat Project" or "ReadMe". With that you can open the frontend of the code.

The function that i added are the History Transactions, everytime you deposit it will bump to history transaction like a database right?, 

## Authors

Metacrafter Jaimmy 


## License

This project is licensed under the MIT License - see the LICENSE.md file for details
