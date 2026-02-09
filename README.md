w# blockchain

 Blockchain Certificate Verification System
(Ganache + Remix + Solidity + Python + Tailwind CSS)

🔰 OVERALL ARCHITECTURE (Explain to Examiner)
Frontend (HTML + Tailwind)
        ↓
Python Backend (Web3.py)
        ↓
Smart Contract (Solidity)
        ↓
Ganache Local Blockchain
1️⃣ INSTALL REQUIRED SOFTWARE (FREE)
✅ Install
Python 3.8+

Ganache
https://trufflesuite.com/ganache/

Remix IDE (Online)
https://remix.ethereum.org/

✅ Install Python Libraries
pip install web3 flask flask-cors
2️⃣ START GANACHE
Open Ganache

Click Quickstart Ethereum

Copy RPC URL:

http://127.0.0.1:7545
Keep Ganache OPEN

3️⃣ SMART CONTRACT (Solidity)
Open Remix → Create File
CertificateVerification.sol

✅ FULL SMART CONTRACT CODE
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CertificateVerification {

    struct Certificate {
        string name;
        string course;
        bool issued;
    }

    mapping(string => Certificate) certificates;

    function issueCertificate(
        string memory _certId,
        string memory _name,
        string memory _course
    ) public {
        certificates[_certId] = Certificate(_name, _course, true);
    }

    function verifyCertificate(string memory _certId)
        public
        view
        returns (string memory, string memory, bool)
    {
        Certificate memory cert = certificates[_certId];
        return (cert.name, cert.course, cert.issued);
    }
}
4️⃣ DEPLOY SMART CONTRACT
Remix Steps:
Solidity Compiler → Compile

Deploy & Run

Environment: Web3 Provider

RPC:

http://127.0.0.1:7545
Click Deploy

Copy:

Contract Address

ABI (from Remix → ABI button)

5️⃣ PROJECT FOLDER STRUCTURE
certificate_dapp/
│
├── backend.py
└── templates/
    └── index.html
6️⃣ BACKEND (Python + Flask + Web3)
🔹 backend.py (FULL CODE)
from flask import Flask, request, jsonify, render_template
from web3 import Web3
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

# Ganache connection
ganache_url = "http://127.0.0.1:7545"
web3 = Web3(Web3.HTTPProvider(ganache_url))

account = web3.eth.accounts[0]
web3.eth.default_account = account

# 🔴 PASTE YOUR DETAILS HERE
contract_address = "PASTE_CONTRACT_ADDRESS"
contract_abi = PASTE_ABI_HERE

contract = web3.eth.contract(
    address=contract_address,
    abi=contract_abi
)

@app.route("/")
def home():
    return render_template("index.html")

@app.route("/issue", methods=["POST"])
def issue():
    data = request.json
    tx = contract.functions.issueCertificate(
        data["certId"],
        data["name"],
        data["course"]
    ).transact()
    web3.eth.wait_for_transaction_receipt(tx)
    return jsonify({"status": "Certificate Issued"})

@app.route("/verify/<certId>")
def verify(certId):
    name, course, issued = contract.functions.verifyCertificate(certId).call()
    return jsonify({
        "name": name,
        "course": course,
        "issued": issued
    })

if __name__ == "__main__":
    app.run(debug=True)
7️⃣ FRONTEND (HTML + Tailwind CDN)
🔹 templates/index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Blockchain Certificate Verification</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>

<body class="bg-gray-100 min-h-screen flex items-center justify-center">

<div class="bg-white p-8 rounded-lg shadow-lg w-full max-w-md">

  <h1 class="text-2xl font-bold text-center mb-6">
    Certificate Verification
  </h1>

  <!-- Issue Certificate -->
  <div class="mb-6">
    <h2 class="font-semibold mb-2">Issue Certificate</h2>
    <input id="certId" class="w-full border p-2 mb-2" placeholder="Certificate ID">
    <input id="name" class="w-full border p-2 mb-2" placeholder="Student Name">
    <input id="course" class="w-full border p-2 mb-2" placeholder="Course">
    <button onclick="issueCert()" class="w-full bg-blue-600 text-white py-2 rounded">
      Issue Certificate
    </button>
  </div>

  <!-- Verify Certificate -->
  <div>
    <h2 class="font-semibold mb-2">Verify Certificate</h2>
    <input id="verifyId" class="w-full border p-2 mb-2" placeholder="Certificate ID">
    <button onclick="verifyCert()" class="w-full bg-green-600 text-white py-2 rounded">
      Verify
    </button>
    <p id="result" class="mt-3 text-center font-semibold"></p>
  </div>

</div>

<script>
function issueCert() {
  fetch("/issue", {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: JSON.stringify({
      certId: certId.value,
      name: name.value,
      course: course.value
    })
  }).then(res => res.json())
    .then(data => alert(data.status));
}

function verifyCert() {
  fetch(`/verify/${verifyId.value}`)
    .then(res => res.json())
    .then(data => {
      result.innerText = data.issued
        ? `✅ VALID\nName: ${data.name}\nCourse: ${data.course}`
        : "❌ INVALID CERTIFICATE";
    });
}
</script>

</body>
</html>
8️⃣ RUN THE PROJECT (END-TO-END)
Step 1
Open Ganache

Step 2
Deploy contract in Remix

Step 3
Paste:

Contract Address

ABI
into backend.py

Step 4
Run backend:

python backend.py
Step 5
Open browser:

http://127.0.0.1:5000
🎉 DONE — Full Blockchain DApp Running!

