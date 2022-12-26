
# Tóm lược 

Thông qua hướng dẫn này bạn sẽ học về cách biên dịch và deploy một hợp đồng thông minh cơ bản. Bạn sẽ được học cách sử dụng các APIs cơ bản của thư viện `web3js`. 

# Chuẩn bị 

- Bạn cần tạo một dự án trên [Infura](https://infura.io) và lấy `PROJECT ID`, thay đổi `ENDPOINTS` thành `Goerli`. 

- Tạo một tài khoản trên `MetaMask`. `MetaMask` là một brower extension: 

    1. Lấy wallet `address` và private key. 
    2. Đi tới `Settings` - `advandced` và mở phần `Show test network`; 
        - Chọn `Goerli`, và lưu lại địa chỉ. 
    3. Nạp tiền cho tài khoản của bạn thông qua [faucets](https://faucets.chain.link) hoặc các dịch vụ tương tự khác. 
    4. Chờ vài phút, kiểm tra số dư trong tài khoản của bạn trên `MetaMask`

# Bắt đầu 

## Tìm hiểu các hàm của [hợp đồng thông minh](Incrementer.sol)
- `Constructor`: khởi tạo hợp đồng thông mình và được gọi khi deploy hợp đồng thông minh, và khởi tạo các giá trị `number` bằng giá trị `_initialNumber`. 
- `increment`: Hàm tăng giá trị của `number` bởi giá trị `_value` được cho. 
- `rest`: Xét giá trị của `number` về 0. 
- `getNumber`: Hàm lấy giá trị của biến `number`.

## Thực thi 

1. Cài đặt dependencies: `npm install`. 
2. Tạo config file: `cp .env.example .env`. 
3. They đổi giá trị của config file cho phù hợp với project của bạn. 