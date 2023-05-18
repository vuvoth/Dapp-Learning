# Abtract
Thông qua những task cơ bản, bạn có thể học được những kiến thức liên quan đến trình biên dịch và quy trình triển khai một Smart Contract, cũng như sử dụng các API cơ bản của `web3js`.
# Chuẩn bị
- Tạo một dự án trên  [Infura](https://infura.io), và chọn `PROJECT ID`, sau đó thay đổi `ENDPOINTS` thành `Goerli`;

- Tạo một account trên trình duyệt mở rộng `MetaMask`;
 1. Chọn `address` của ví và khóa riêng tư (`private key`);
 2. Đến `Setting` - `advanced` và mở `Show test networks`;
     - Chọn `Goerli` và ghi nhận địa chỉ
 3. Nạp tiền vào tài khoản thông qua  [faucets](https://faucets.chain.link) hoặc các web dịch vụ khác;
 4. Sau vài phút có thể kiểm tra số dư trên `MetaMask`

- Tạo file `.env` và thêm các dòng sau:
  
    PRIVATE_KEY=YOUR_PRIVATE_KEY
    INFURA_ID=YOUR_PROJECT_ID
   
    
  | Ghi chú: Bạn có thể kiểm tra file `.env.example`
 
 - Nếu bạn biết tiếng Trung bạn có thể kiểm tra các hoạt động này trên [BILIBILI](https://www.bilibili.com/video/BV1Y44y1r7E6/).
 
 # Bắt Đầu
 
 ## Hàm (functions) trong [Smart Contract](Incrementer.sol)
 - `Constructor`: Hàm khởi tạo (Constructor Function) được gọi khi smart contract được triển khai, đồng thời sẽ khởi tạo `number` thành `_initialNumber`;
 - `increment`: hàm gia tăng `number` từ `value` đã cho
 - `rest` : Hàm đặt giá trị của `number` về 0 
 - `getNumber` : Hàm lấy số
 
 ## Khởi động
 
 1. Các cài đặt phụ thuộc: `npm install`
 2. Sao chép cấu hình file: `cp .env.example .env`
 3. Chỉnh sử cấu hình file thành `vim .env`, sao chép ID dự án và khóa riêng tư vào file `.env`
 
  PRIVATE_KEY=YOUR_PRIVATE_KEY
  INFURA_ID=YOUR_PROJECT_ID
    
 4. Khởi động file 
 
 # Giải nghĩa code trong `index.js`
 `index.js` chứa đựng các chức năng quan trọng nhất bao gồm:
 ## 1. Load cấu hình file
Bởi vì mục đích bảo mật nên private key không được xem như là một hard coded, tuy nhiên nó vẫn có thể được đọc như những biến môi trường. 
Khi khởi động task, plugin `dotenv` tự động đọc các cấu hình trên file `.env` và load chúng dưới dạng các biến môi trường, sau đó có thể sử dụng các `private key` và các biến môi trường khác thông qua `process.env` .
Code: 
```js
require("dotenv").config();
const privatekey = process.env.PRIVATE_KEY;
```

## 2. Biên dịch file smart contract 
Không thể trực tiếp sử dụng các file `.sol` mà trước tiên phải biên dịch chúng về dạng nhị phân.
### Load file smart contract `Incrementer.sol` thành biến `source`
```js
// Load contract
const source = fs.readFileSync("Incrementer.sol", "utf8");
```
#### Biên dịch file smart contract

```js
const input = {
  language: "Solidity",
  sources: {
    "Incrementer.sol": {
      content: source,
    },
  },
  settings: {
    outputSelection: {
      "*": {
        "*": ["*"],
      },
    },
  },
};

const tempFile = JSON.parse(solc.compile(JSON.stringify(input)));
```
| Note: Bản cập nhật Solidity `0.8.0`, trình biên dịch ở mỗi bản cập nhật là khác nhau.

## 3. Cách lấy `bytecode` và `abi`
```js
const contractFile = tempFile.contracts["Incrementer.sol"]["Incrementer"];

// Get bin & abi
const bytecode = contractFile.evm.bytecode.object;
const abi = contractFile.abi;
```  

## 4. Cách tạo instance `web3`
`web3` là API chính của thư viện `web3js`, được dùng để tương tác với blockchain.
```js
// Create web3 with goerli provider，you can change goerli to other testnet
const web3 = new Web3(
  "https://goerli.infura.io/v3/" + process.env.INFURA_ID
);
```
| Note: `INFURA_ID` chính là `PROJECT ID` của dự án `Infura` mà bạn đã tạo trong phần **Chuẩn bị**

## 5. Cách lấy địa chỉ `account`
Mỗi blockchain sẽ cung cấp cho người dùng một `address` duy nhất thông qua private key. Bạn có thể sử dụng API `we3.eth.accounts.privateKeyToAccount` để lấy địa chỉ `account` bằng cách chuyển private key thành một parameter.
```js
// Tạo account từ privatekey
const account = web3.eth.accounts.privateKeyToAccount(privatekey);
const account_from = {
  privateKey: privatekey,
  accountAddress: account.address,
};
```

## 6. Cách nhận contract instance
Sau khi đã lấy `bytecode` và `abi` ở bước thứ ba thì chúng ta có thể tạo contract instance bằng `abi`

```js
// Tạo contract instance
const deployContract = new web3.eth.Contract(abi);
```

## 7. Tạo `deploy` transaction
```js
// Tạo Tx
const deployTx = deployContract.deploy({
    data: bytecode,
    arguments: [5],
});

```
## 8. Ký `deploy` transaction
Sử dụng private key để ký `deploy` transaction. 
```js
const deployTransaction = await web3.eth.accounts.signTransaction(
    {
        data: deployTx.encodeABI(),
        gas: 8000000,
    },
    account_from.privateKey
);
```

## 9 Deploy smart contract
Gửi `deploy` transaction đến bockchian bạn sẽ nhận lại một biên lai và lấy địa chỉ hợp đồng từ đây. 
```js
const deployReceipt = await web3.eth.sendSignedTransaction(
    deployTransaction.rawTransaction
);
console.log(`Contract deployed at address: ${deployReceipt.contractAddress}`);
```

# Nguồn tài liệu
- Web3js Official Documents: https://web3js.readthedocs.io/en/v1.2.11/getting-started.html
- Code and Examples: https://docs.moonbeam.network/getting-started/local-node/deploy-contract/ 
- How to use web3js: https://www.dappuniversity.com/articles/web3-js-intro
- Nodejs APIs Documents: http://nodejs.cn/api/fs.html








 
 
 
 
