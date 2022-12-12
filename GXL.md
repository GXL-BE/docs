### APIs

#### 0. Auth

- We use HTTP Header to identity user: `X-Token`
- `X-Token` is JWT access token, issue by GXL authentication backend (`RS256`)

```json
{
  "iat": 1516239022,
  "exp": 1516339022
}
```

#### 1. Get Address To Deposit

- Description: Sử dụng để nạp tài sản vào hệ thống, VD rút từ Binance, Houbi, ... về
- Endpoint: `/deposit/address/${user_id}`
- Method: `POST`
- Params: `JSON Object`

| Param     | Type     | Required? | Description                                |
| --------- | -------- | --------- | ------------------------------------------ |
| `asset`   | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network` | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |

- Response: `JSON Object`

| Name      | Type      | Description                         |
| --------- | --------- | ----------------------------------- |
| `status`  | `Boolean` | Trạng thái giao dịch                |
| `address` | `String`  | Địa chỉ nạp tài sản                 |
| `user_id` | `String`  | ID của user                         |
| `network` | `String`  | [Mạng quản lý tài sản](#chain-list) |
| `asset`   | `String`  | Tài sản muốn thế chấp               |

#### 2. Map user address

- Description: Sử dụng để map 1 address dex với 1 user
- Endpoint: `/deposit/address/mapping`
- Method: `POST`
- Params: `JSON Object`

| Param       | Type     | Required? | Description                                                               |
| ----------- | -------- | --------- | ------------------------------------------------------------------------- |
| `wallet`    | `String` | `[x]`     | Địa chỉ wallet                                                            |
| `msg`       | `String` | `[x]`     | Nội dung message được kí                                                  |
| `signature` | `String` | `[x]`     | Message được kí bằng ví user. Format userId,expire. Ví dụ: 123,1657251084 |
| `network`   | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)                                       |

- Response: `JSON Object`

| Name      | Type      | Description                         |
| --------- | --------- | ----------------------------------- |
| `status`  | `Boolean` | Trạng thái giao dịch                |
| `address` | `String`  | Địa chỉ nạp tài sản                 |
| `user_id` | `String`  | ID của user                         |
| `network` | `String`  | [Mạng quản lý tài sản](#chain-list) |

#### 3. Get Smart Contract To Deposit

- Description: Sử dụng để gọi hàm `deposit(address asset, uint amount)` nạp tài sản vào hệ thống
- Endpoint: `/deposit/pool/${user_id}`
- Method: `POST`
- Params: `JSON Object`

| Param     | Type     | Required? | Description                                |
| --------- | -------- | --------- | ------------------------------------------ |
| `asset`   | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network` | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |

- Response: `JSON Object`

| Name      | Type      | Description                         |
| --------- | --------- | ----------------------------------- |
| `status`  | `Boolean` | Trạng thái giao dịch                |
| `pool`    | `String`  | Địa chỉ smart contract pool         |
| `user_id` | `String`  | ID của user                         |
| `network` | `String`  | [Mạng quản lý tài sản](#chain-list) |
| `asset`   | `String`  | Tài sản muốn thế chấp               |

#### 4. Get User Balances

- Description: Sử dụng để lấy số dư tài sản của user
- Endpoint: `/balances/${user_id}`
- Method: `GET`
- Response: `JSON Object`

| Name                      | Type               | Description                         |
| ------------------------- | ------------------ | ----------------------------------- |
| `status`                  | `Boolean`          | Trạng thái giao dịch                |
| `user_id`                 | `String`           | ID của user                         |
| `balances`                | `Array of Objects` | Danh sách các khoản dư              |
| `balances`.[].`locked`    | `Number`           | Số dư bị khoá do thế chấp           |
| `balances`.[].`available` | `Number`           | Số dư khả dụng                      |
| `balances`.[].`asset`     | `String`           | Tài sản                             |
| `balances`.[].`network`   | `String`           | [Mạng quản lý tài sản](#chain-list) |

#### 5. Lock User Balance

- Description: Sử dụng để lock số dư của user,
- Endpoint: `/balances/${user_id}/lock`
- Method: `POST`
- Request: `JSON Object`

| Param              | Type     | Required? | Description                                |
| ------------------ | -------- | --------- | ------------------------------------------ |
| `asset`            | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network`          | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |
| `amount`           | `Number` | `[x]`     | Số lượng asset cần lock                    |
| `ref`              | `String` |           | Referer                                    |
| `reason`           | `String` |           | Detail nguyên nhân lock                    |
| `notification_url` | `String` |           | URL nhận thông tin sau khi unlock          |

- Response: `JSON Object`

| Name        | Type      | Description                                 |
| ----------- | --------- | ------------------------------------------- |
| `status`    | `Boolean` | Trạng thái giao dịch                        |
| `network`   | `String`  | [Mạng quản lý tài sản](#chain-list)         |
| `lock_id`   | `String`  | ID của lock, được sử dụng để release về sau |
| `asset`     | `String`  | Tài sản muốn thế chấp                       |
| `user_id`   | `String`  | ID của user                                 |
| `available` | `Number`  | Số dư khả dụng                              |
| `locked`    | `Number`  | Số dư bị khoá do thế chấp                   |

#### 6. Unlock User Balance

- Description: Sử dụng để unlock khoản vay của user
- Endpoint: `/balances/${user_id}/unlock`
- Method: `POST`
- Request: `JSON Object`

| Param              | Type     | Required? | Description                                                                              |
| ------------------ | -------- | --------- | ---------------------------------------------------------------------------------------- |
| `asset`            | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ...                                               |
| `network`          | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)                                                      |
| `lock_id`          | `String` | `[x]`     | ID của lệnh lock                                                                         |
| `amount`           | `Number` | `[x]`     | Số lượng asset unlock                                                                    |
| `fee`              | `Number` | `[x]`     | Fee thu được của user                                                                    |
| `withdraw_address` | `String` |           | Địa chỉ rút asset của user, nếu địa chỉ này trống, `amount` sẽ được cộng vào `available` |
| `ref`              | `String` |           | Referer                                                                                  |
| `reason`           | `String` |           | Detail nguyên nhân unlock                                                                |
| `notification_url` | `String` |           | URL nhận thông tin sau khi unlock                                                        |

- Response: `JSON Object`

| Name            | Type      | Description                                                                |
| --------------- | --------- | -------------------------------------------------------------------------- |
| `status`        | `Boolean` | Trạng thái giao dịch                                                       |
| `network`       | `String`  | [Mạng quản lý tài sản](#chain-list)                                        |
| `unlock_id`     | `String`  | ID của unlock, được sử dụng để tra soát về sau                             |
| `withdraw_txid` | `String`  | TXID của giao dịch withdraw của user (nếu `withdraw_address` được sử dụng) |
| `asset`         | `String`  | Tài sản muốn thế chấp                                                      |
| `user_id`       | `String`  | ID của user                                                                |
| `available`     | `Number`  | Số dư khả dụng                                                             |
| `locked`        | `Number`  | Số dư bị khoá do thế chấp                                                  |

#### 7. Liquidate User Lock

- Description: Sử dụng để thanh lý khoản vay của user
- Endpoint: `/balances/${user_id}/liquidate`
- Method: `POST`
- Request: `JSON Object`

| Param              | Type     | Required? | Description                                |
| ------------------ | -------- | --------- | ------------------------------------------ |
| `asset`            | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network`          | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |
| `amount`           | `Number` | `[x]`     | Số lượng asset cần thanh lý                |
| `lock_id`          | `String` | `[]`      | ID của lệnh lock                           |
| `ref`              | `String` |           | Referer                                    |
| `reason`           | `String` |           | Detail nguyên nhân lock                    |
| `notification_url` | `String` |           | URL nhận thông tin sau khi thanh lý        |

- Response: `JSON Object`

| Name           | Type     | Description                                           |
| -------------- | -------- | ----------------------------------------------------- |
| `status`       | `String` | Trạng thái giao dịch                                  |
| `network`      | `String` | [Mạng quản lý tài sản](#chain-list)                   |
| `liquidate_id` | `String` | ID của lệnh thanh lý, được sử dụng để tra soát về sau |
| `asset`        | `String` | Tài sản muốn thế chấp                                 |
| `user_id`      | `String` | ID của user                                           |
| `available`    | `Number` | Số dư khả dụng                                        |
| `locked`       | `Number` | Số dư bị khoá do thế chấp                             |

#### 8. Withdraw free/available balance

- Description: Sử dụng để gửi yêu cầu rút tài sản không bị khoá
- Endpoint: `/balances/${user_id}/withdraw`
- Method: `POST`
- Request: `JSON Object`

| Param              | Type     | Required? | Description                                |
| ------------------ | -------- | --------- | ------------------------------------------ |
| `asset`            | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network`          | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |
| `amount`           | `Number` | `[x]`     | Số lượng asset unlock                      |
| `fee`              | `Number` | `[x]`     | Fee thu được của user                      |
| `to`               | `String` | `[x]`     | Địa chỉ rút asset của user                 |
| `ref`              | `String` |           | Referer                                    |
| `reason`           | `String` |           | Detail nguyên nhân unlock                  |
| `notification_url` | `String` |           | URL nhận thông tin sau khi unlock          |

- Response: `JSON Object`

| Name     | Type      | Description          |
| -------- | --------- | -------------------- |
| `status` | `Boolean` | Trạng thái giao dịch |

#### 9. Get User Balance OnChain

- Description: Sử dụng để lấy số dư onchain của user
- Endpoint: `/balances/${address}`
- Method: `POST`
- Request: `JSON Object`

| Param     | Type              | Required? | Description                                |
| --------- | ----------------- | --------- | ------------------------------------------ |
| `assets`  | `Array of String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network` | `String`          | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |

- Response: `JSON Object`

| Name                 | Type               | Description                         |
| -------------------- | ------------------ | ----------------------------------- |
| `status`             | `Boolean`          | Trạng thái giao dịch                |
| `network`            | `String`           | [Mạng quản lý tài sản](#chain-list) |
| `assets`             | `Array of Objects` | Danh sách tài sản on-chain          |
| `assets`.[].`amount` | `Number`           | Số dư tài sản hiện có               |
| `assets`.[].`asset`  | `String`           | Tên tài sản                         |

#### 10. Register check transaction

- Description: Sử dụng để gửi yêu cầu xác thực giao dịch khi user chuyển tài sản cho nhau
- Endpoint: `/transaction/{$txid}`
- Method: `POST`
- Request: `JSON Object`

| Param              | Type     | Required? | Description                                        |
| ------------------ | -------- | --------- | -------------------------------------------------- |
| `asset`            | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ...         |
| `network`          | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)                |
| `from`             | `String` |           | Địa chỉ chuyển tài sản                             |
| `to`               | `String` | `[x]`     | Địa chỉ chuyển tài sản                             |
| `notification_url` | `String` | `[x]`     | URL nhận thông tin sau khi giao dịch được xác nhận |

- Response: `JSON Object`

| Name     | Type      | Description          |
| -------- | --------- | -------------------- |
| `status` | `Boolean` | Trạng thái giao dịch |

#### 11. Get base withdraw fee

- Description: Sử dụng để lấy fee
- Endpoint: `/balances/${user_id}/estimate`
- Method: `POST`
- Request: `JSON Object`

| Param     | Type     | Required? | Description                                |
| --------- | -------- | --------- | ------------------------------------------ |
| `asset`   | `String` | `[x]`     | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network` | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list)        |
| `amount`  | `Number` | `[x]`     | Số lượng asset unlock                      |
| `to`      | `String` | `[x]`     | Địa chỉ rút asset của user                 |

- Response: `JSON Object`

| Name           | Type      | Description          |
| -------------- | --------- | -------------------- |
| `status`       | `Boolean` | Trạng thái giao dịch |
| `gas_price`    | `Number`  | Phí gas tối thiểu    |
| `estimate_gas` | `Number`  | Gas ước tính         |

#### 12. Sync tx hash

- Description: Sử dụng để sync tx on chain
- Endpoint: `/sync/tx`
- Method: `POST`
- Request: `JSON Object`

| Param     | Type     | Required? | Description                         |
| --------- | -------- | --------- | ----------------------------------- |
| `tx_hash` | `String` | `[x]`     | On chain transaction hash           |
| `network` | `String` | `[x]`     | [Mạng quản lý tài sản](#chain-list) |

- Response: `JSON Object`

| Name     | Type      | Description          |
| -------- | --------- | -------------------- |
| `status` | `Boolean` | Trạng thái giao dịch |

### IPNs

#### 1. Lock User Balance

- Description: Sử dụng để gửi trạng thái lock tài sản của user
- Endpoint: `notification_url` trong request
- Method: `POST`
- Request: `JSON Object`

| Param       | Type      | Description                                |
| ----------- | --------- | ------------------------------------------ |
| `status`    | `Boolean` | Trạng thái của action                      |
| `asset`     | `String`  | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network`   | `String`  | [Mạng quản lý tài sản](#chain-list)        |
| `amount`    | `Number`  | Số lượng asset cần lock                    |
| `lock_id`   | `String`  | ID của action lock                         |
| `ref`       | `String`  | Referer                                    |
| `user_id`   | `String`  | ID của user                                |
| `available` | `Number`  | Số dư khả dụng                             |
| `locked`    | `Number`  | Tổng tài sản đã khoá do thế chấp           |

#### 2. Unlock User Balance

- Description: Sử dụng để gửi trạng thái unlock tài sản của user
- Endpoint: `notification_url` trong request
- Method: `POST`
- Request: `JSON Object`

| Param       | Type      | Description                                             |
| ----------- | --------- | ------------------------------------------------------- |
| `status`    | `Boolean` | Trạng thái của action                                   |
| `asset`     | `String`  | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ...              |
| `network`   | `String`  | [Mạng quản lý tài sản](#chain-list)                     |
| `amount`    | `Number`  | Số lượng asset unlock                                   |
| `lock_id`   | `String`  | ID của action lock                                      |
| `unlock_id` | `String`  | ID của action unlock                                    |
| `ref`       | `String`  | Referer                                                 |
| `user_id`   | `String`  | ID của user                                             |
| `available` | `Number`  | Số dư khả dụng                                          |
| `locked`    | `Number`  | Tổng tài sản đã khoá do thế chấp                        |
| `txid`      | `String`  | TXID của giao dịch rút tài sản trên blockchain (nếu có) |

#### 3. Liquidate User Lock

- Description: Sử dụng để gửi trạng thái thanh lý tài sản của user
- Endpoint: `notification_url` trong request
- Method: `POST`
- Request: `JSON Object`

| Param          | Type      | Description                         |
| -------------- | --------- | ----------------------------------- |
| `status`       | `Boolean` | Trạng thái của action               |
| `network`      | `String`  | [Mạng quản lý tài sản](#chain-list) |
| `lock_id`      | `String`  | ID của action lock                  |
| `liquidate_id` | `String`  | ID của action thanh lý              |
| `ref`          | `String`  | Referer                             |
| `user_id`      | `String`  | ID của user                         |
| `available`    | `Number`  | Số dư khả dụng                      |
| `locked`       | `Number`  | Tổng tài sản đã khoá do thế chấp    |
| `asset`        | `String`  | Tài sản thanh lý                    |
| `amount`       | `Number`  | Số lượng tài sản thanh lý           |
| `amount_out`   | `Number`  | Số lượng tài sản sau thanh lý       |
| `asset_out`    | `String`  | Loại tài sản thanh lý               |
| `fee`          | `Object`  | Phí thanh lý                        |
| `fee.asset`    | `String`  | Loại tài sản của phí thanh lý       |
| `fee.amount`   | `Number`  | Số lượng phí thanh lý               |
| `chain_txid`   | `String`  | Transaction id trên chain           |


#### 4. Withdraw free/available balance

- Description: Sử dụng để gửi trạng thái rút tài sản khả dụng của user
- Endpoint: `notification_url` trong request
- Method: `POST`
- Request: `JSON Object`

| Param        | Type      | Description                                |
| ------------ | --------- | ------------------------------------------ |
| `status`     | `Boolean` | Trạng thái của action                      |
| `asset`      | `String`  | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network`    | `String`  | [Mạng quản lý tài sản](#chain-list)        |
| `amount`     | `Number`  | Số lượng asset rút ra ngoài                |
| `fee`        | `Number`  | Fee thu được của user                      |
| `to`         | `String`  | Địa chỉ rút tài sản                        |
| `txid`       | `String`  | TXID của giao dịch chuyển tài sản          |
| `ref`        | `String`  | Referer                                    |
| `user_id`    | `String`  | ID của user                                |
| `available`  | `Number`  | Số dư khả dụng                             |
| `locked`     | `Number`  | Tổng tài sản đã khoá do thế chấp           |
| `chain_txid` | `String`  | Transaction id trên chain                  |


#### 5. Register check transaction

- Description: Sử dụng để gửi trạng thái rút tài sản khả dụng của user
- Endpoint: `notification_url` trong request
- Method: `POST`
- Request: `JSON Object`

| Param     | Type      | Description                                |
| --------- | --------- | ------------------------------------------ |
| `status`  | `Boolean` | Trạng thái của giao dịch                   |
| `asset`   | `String`  | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network` | `String`  | [Mạng quản lý tài sản](#chain-list)        |
| `amount`  | `Number`  | Số lượng asset được giao dịch              |
| `from`    | `String`  | Địa chỉ người gửi                          |
| `to`      | `String`  | Địa chỉ người nhận                         |
| `txid`    | `String`  | TXID của giao dịch cần kiểm tra            |

#### 6. onDeposit

- Description: Khi user depoit
- Endpoint: Được đăng ký trước
- Method: `POST`
- Request: `JSON Object`

| Param       | Type     | Description                                |
| ----------- | -------- | ------------------------------------------ |
| `asset`     | `String` | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `network`   | `String` | [Mạng quản lý tài sản](#chain-list)        |
| `amount`    | `Number` | Số lượng asset deposit                     |
| `to`        | `String` | Địa chỉ user                               |
| `txid`      | `String` | TXID của giao dịch nhận deposit            |
| `tx_hash`   | `String` | TX hash của giao dịch nhận deposit         |
| `user_id`   | `String` | ID của user                                |
| `available` | `Number` | Số dư khả dụng                             |
| `locked`    | `Number` | Tổng tài sản đã khoá do thế chấp           |

### 7. onPriceUpdated

- Description: Khi giá asset thay đổi
- Endpoint: Được đăng ký trước
- Method: `POST`
- Request: `JSON Object`

| Param       | Type     | Description                                |
| ----------- | -------- | ------------------------------------------ |
| `asset`     | `String` | Tài sản muốn thế chấp, VD `ETH`,`BNB`, ... |
| `price`     | `Number` | Giá cũ                                     |
| `p1h`       | `Number` | Phần trăm 1h                               |
| `p24h`      | `Number` | Phần trăm 24h                              |
| `p7d`       | `Number` | Phần trăm 7d                               |
| `volume`    | `Number` | Khối lượng giao dịch                       |
| `timestamp` | `Number` | Thời gian                                  |


### Get Transaction Info

- Description: Lấy trạng thái thông tin giao dịch theo transaction id / liquidiate id
- Endpoint: `/transaction/${tx_id}`
- Method: `GET`
- Response: `JSON Object`

| Name                 | Type      | Description               |
| -------------------- | --------- | ------------------------- |
| `status`             | `Boolean` | Trạng thái giao dịch      |
| `from`               | `Address` | Địa chỉ tạo giao dịch     |
| `to`                 | `Address` | Địa chỉ nhận              |
| `amount`             | `String`  | Số tiền giao dịch         |
| `network`            | `String`  | Chain giao dịch           |
| `asset`              | `String`  | Tài sản giao dịch         |
| `transaction_status` | `String`  | Trạng thái giao dịch      |
| `txid`               | `String`  | Transaction id            |
| `chain_txid`         | `String`  | Transaction id trên chain |
| `type`               | `String`  | Loại giao dịch            |
| `ref`                | `String`  | Refer của giao dịch       |
| `reason`             | `String`  | Lý do của giao dịch       |

```json
{
    "status": true,
    "from": "0x0638B9D4597c5A9a248C619a5110D094a1236909",
    "to": null,
    "amount": "0.2",
    "network": "bsc testnet",
    "asset": "MUSD",
    "transaction_status": "SUCCESS",
    "txid": "2a746c53-ba87-4719-83eb-136610483840",
    "chain_txid": "0xd8bc801e9f09acf5667c067cb9933bee7dcfd4d382245dce18b45a5e73b0ccf8",
    "type": "LIQUIDATE",
    "ref": null,
    "reason": null
}
```


### Chain list

```javascript
export type ChainList =
  | "eth"
  | "0x1"
  | "ropsten"
  | "0x3"
  | "rinkeby"
  | "0x4"
  | "goerli"
  | "0x5"
  | "kovan"
  | "0x2a"
  | "polygon"
  | "0x89"
  | "mumbai"
  | "0x13881"
  | "bsc"
  | "0x38"
  | "bsc testnet"
  | "0x61"
  | "avalanche"
  | "0xa86a"
  | "avalanche testnet"
  | "0xa869"
  | "fantom"
  | "0xfa"
  | "cronos"
  | "0x19";
```

#### Get List Assets
- Description: Danh sách các token hệ thống hỗ trợ
- Endpoint: `/assets`
- Method: `POST`
- Response: `JSON Object`

| Name                         | Type           | Description                     |
| ---------------------------- | -------------- | ------------------------------- |
| `status`                     | `Boolean`      | Trạng thái giao dịch            |
| `asset`                      | `Array Object` |                                 |
| `asset[].name`               | `String`       | Tên Token                       |
| `asset[].symbol`             | `String`       | Symbol của Token                |
| `asset[].decimals`           | `Number`       | Số decimals của token           |
| `asset[].identity`           | `String`       | Ký hiệu định danh Token         |
| `asset[].chains`             | `Array Object` |                                 |
| `asset[].chains[].name`      | `String`       | Tên của chain                   |
| `asset[].chains[].address`   | `String`       | Địa chỉ token trên chain        |
| `asset[].chains[].chainId`   | `String`       | Id của chain                    |
| `asset[].chains[].isMainnet` | `Boolean`      | true = mainnet, false = testnet |
| `asset[].chains[].chainType` | `String`       | EIP155,...              |

```json
{
    "status": true,
    "assets": [
        {
            "name": "BNB",
            "symbol": "BNB",
            "decimals": 18,
            "identity": "BNBTESTNET",
            "chains": [
                {
                    "name": "bsc testnet",
                    "address": "0x0000000000000000000000000000000000000000",
                    "chainId": "97",
                    "isMainnet": false,
                    "chainType": "EIP155",
                    "isNative": true
                }
            ]
        },
        {
            "name": "MUSD Testnet",
            "symbol": "MUSD",
            "decimals": 18,
            "identity": "MUSDTESTNET",
            "chains": [
                {
                    "name": "bsc testnet",
                    "address": "0xFFCd573783A0335CE6591E78592b6F93B1A4e329",
                    "chainId": "97",
                    "isMainnet": false,
                    "chainType": "EIP155"
                }
            ]
        }
    ]
}
```

## DEX
### DEX Function
- Deposit từ ví DEX (Metamask,...) bằng cách gọi hàm `depositERC20` đến contract.
- Bank Contract: `0x0132ab5bdc4585aa5540B23D419A40edfdb5C43E`
- Function: 
```
  function depositERC20(
        address erc20,
        uint256 amount,
        address depositer,
        address app,
        string calldata reason
    ) public payable returns (uint256)
```
- Native Coin: address erc20 = `0x0000000000000000000000000000000000000000`


## Others

### Mint ERC20 Test

MUSDTESTNET: [MUSD BSC TESTNET MINT](https://testnet.bscscan.com/address/0xFFCd573783A0335CE6591E78592b6F93B1A4e329#writeContract#F4)
