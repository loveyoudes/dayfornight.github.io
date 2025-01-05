<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Solana 签到系统</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 50px;
    }
    button {
      padding: 10px 20px;
      margin: 20px;
      font-size: 16px;
      cursor: pointer;
    }
    #status {
      font-size: 18px;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <h1>Solana 签到系统</h1>
  <p>每日签到领取代币！</p>
  <button id="connectButton">连接钱包</button>
  <button id="signInButton" style="display:none;">签到</button>
  <div id="status"></div>

  <script src="https://cdn.jsdelivr.net/npm/@solana/web3.js@1.31.0/dist/web3.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@solana/wallet-adapter-wallets@0.10.0/dist/index.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@solana/wallet-adapter-react@0.10.0/dist/index.min.js"></script>

  <script>
    let connection;
    let provider;
    let wallet;
    let userPublicKey;
    let signInButton;
    let connectButton;

    // 连接钱包
    document.getElementById('connectButton').onclick = async () => {
      // 检查是否安装了Phantom钱包
      if (window.solana && window.solana.isPhantom) {
        wallet = window.solana;
        await wallet.connect();
        userPublicKey = wallet.publicKey.toString();
        connection = new solanaWeb3.Connection(solanaWeb3.clusterApiUrl('devnet'), 'confirmed');
        document.getElementById('connectButton').style.display = 'none';
        document.getElementById('signInButton').style.display = 'block';
        updateStatus('钱包已连接: ' + userPublicKey);
      } else {
        alert('请安装Phantom钱包');
      }
    };

    // 签到按钮
    document.getElementById('signInButton').onclick = async () => {
      const programId = "<YOUR_PROGRAM_ID>";  // 填入你自己的智能合约ID
      const transaction = new solanaWeb3.Transaction();
      const signInInstruction = new solanaWeb3.TransactionInstruction({
        keys: [
          { pubkey: new solanaWeb3.PublicKey(userPublicKey), isSigner: true, isWritable: true }
        ],
        programId: new solanaWeb3.PublicKey(programId),
        data: Buffer.from([]),
      });

      transaction.add(signInInstruction);
      try {
        const signedTransaction = await wallet.signTransaction(transaction);
        const txid = await connection.sendRawTransaction(signedTransaction.serialize());
        await connection.confirmTransaction(txid);
        updateStatus('签到成功！交易ID: ' + txid);
      } catch (error) {
        console.error(error);
        updateStatus('签到失败，请重试。');
      }
    };

    // 更新状态显示
    function updateStatus(message) {
      document.getElementById('status').textContent = message;
    }
  </script>
</body>
</html>
