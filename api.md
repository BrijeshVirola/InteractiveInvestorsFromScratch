# Balance Service
The `Balance Service` is responsible for retrieving and modifying balance as well as retrieving the transactions created by adjusting the balance.

[[_TOC_]]

## Protocol

Requests and responses are sent using the HTTP protocol.

### Request

#### URL

POST /api/methodname

#### Headers

The following headers are required

| header | value |
|----|----|
|Accept|application/json|
|Content-Type|application/json|

#### Body

```json
{
    "method": "methodname",
    "id": "someID",
    "params": {
        "param_name": 1234
    }
}
```

All request bodies should be serialised as JSON

A request Object is sent to the target service and contains the following members

| parameter | type | required |
|----|----|----|
|method|string|The name of the method to be invoked||
|id|string|An identifier established by the client that must contain a unique identifier for the request|optional|
|params|json|A JSON Object that holds the parameter values to be used during the invocation of the Method||

### Response

The HTTP status code will always be `200 (OK)`

#### Body

A response Object is sent in reply to a request and contains the following members:

| parameter | type | description | required |
|----|----|----|----|
|id|string|Always null||
|result|json|A JSON object containing the result of the operation|optional - returned on success|
|error|json|A JSON object containing the details of the error|optional - returned on error|

##### Success

Could be of any type e.g. boolean, JSON object, numeric value or string

```json
{
  "id": null,
  "result": {
    "someValue": 10,
    "someOtherValue": true,
    "stringyValue": "I am text"
  }
}
```

##### Failure

```json
{
  "id": null,
  "error": {
    "code": 1007,
    "message": "Unspecified error"
  }
}
```

| parameter | type | notes |
|----|----|----|
|code|int|Numeric code identifying the error|
|message|string|Description of the error|

###### Error codes
The service can return the following error codes

|Code|Description|
|-----|-----|
|1|The request wasn't sent using POST|
|2|The content type header was missing or invalid|
|3|The accept header was missing or invalid|
|4|The JSON in the body was missing or invalid|
|5|The JSON in the body was missing a required field|
|6|The JSON in the body contained an invalid method|
|7|A required parameter is missing from the params object|
|1001|User not found|
|1002|Unexpected error|
|1004|Transaction failed for unknown reason (network error, duplicate id, etc.)|
|1005|Insufficient funds|
|1006|Invalid transfer amount (positive on a stake, negative on a return, total doesn't add up etc.)|
|1007|Transfer calculation failed (Error code returned from proc, something didn't add up correctly)|
|1008|Unrecognised error code (Error code returned from proc wasn't recognised)|
|1009|Invalid transaction type|
|1010|Transaction not found|
|1011|Invalid parameter value (number too big, string too long, etc.)|

## Response models

### Transaction select response 

```json
{
  "id": null,
  "result": {
    "bet365_games_transaction_id": 2202509630,
    "transaction_type_id": 2,
    "action_type_id": 1,
    "partner_transaction_id": "04/08/2021 Jason 1",
    "real_amount": "-3.5",
    "ring_fenced_amount": "0",
    "bonus_amount": "0",
    "total_amount": "-3.5",
    "total_amount_gbp": "-3.5",
    "currency_code": "GBP",
    "partner_timestamp_utc": "2021-08-04T10:42:44.393Z",
    "game_round_id": 1,
    "bet365_transaction_id": 0,
    "provider_region_id": 20,
    "source_transaction_id": 0,
    "regulated_game_id": 6403,
    "user_token": "private token",
    "flake_id": 9148077483121591870,
    "is_new": false
  }
}
```

| field | type | notes |
|----|----|----|
|bet365_games_transaction_id|int64|TransactionID from bet365GamesTransactions table|
|transaction_type_id|int32||
|action_type_id|int32||
|partner_transaction_id|string||
|real_amount|string (decimal)||
|ring_fenced_amount|string (decimal)||
|bonus_amount|string (decimal)||
|total_amount|string (decimal)||
|total_amount_gbp|string (decimal)||
|currency_code|string||
|partner_timestamp_utc|string (timestamp)||
|game_round_id|int64||
|bet365_transaction_id|int64||
|provider_region_id|int32||
|source_transaction_id|int64||
|regulated_game_id|int32||
|user_token|string||
|flake_id|int64||
|is_new|bool|Only true if the transaction was inserted through this request|

### Transaction insert response

```json
{
    "id": null,
    "result": {
        "bet365_games_transaction_id": 2202509630,
        "transaction_type_id": 2,
        "action_type_id": 1,
        "partner_transaction_id": "04/08/2021 Jason 1",
        "real_amount": "-3.5",
        "ring_fenced_amount": "0",
        "bonus_amount": "0",
        "total_amount": "-3.5",
        "total_amount_gbp": "-3.5",
        "currency_code": "GBP",
        "partner_timestamp_utc": "2021-08-04T10:42:44.393Z",
        "game_round_id": 1,
        "bet365_transaction_id": 0,
        "provider_region_id": 20,
        "is_new": true
    }
}
```

| field | type | notes |
|----|----|----|
|bet365_games_transaction_id|int64||
|transaction_type_id|int32||
|action_type_id|int32||
|partner_transaction_id|string||
|real_amount|Decimal||
|ring_fenced_amount|Decimal||
|bonus_amount|string (decimal)||
|total_amount|string (decimal)||
|total_amount_gbp|string (decimal)||
|currency_code|string||
|partner_timestamp_utc|string (timestamp)||
|game_round_id|int64||
|bet365_transaction_id|int64||
|provider_region_id|int32||
|is_new|bool||

## Endpoints

### POST /api/GetUserBalance

    Retrieve a user's balance

#### Request

```json
{
  "Method": "getuserbalance",
  "Params": {
    "user_id": 1030979,
    "product_id": 4
  }
}
```

| parameter | type | description | 
|----|----|----|
|user_id|int32|The user ID|
|product_id|int32|The product ID|
|has_gbt_bonus|bool|Whether you know that the user has a GBT bonus. If `true`, then GBT bonus balances are returned. If `false` then the members bonus balances are returned|

#### Response

```json
{
    "id": null,
    "result": {
        "user_id": 1030979,
        "balance": "1058002.32",
        "bonus": "0",
        "ring_fenced": "0"
    }
}
```

| field | type | notes |
|----|----|----|
|user_id|int32||
|balance|string (decimal)|The product balance|
|bonus|string (decimal)|The product bonus balance|
|ring_fenced|string (decimal)|The product ring fenced balance|

### POST /api/AdjustBalance

    Adjusts a user's balance through inserting a transaction record. 
    Has different behaviour based on the transaction type used

#### Request

```json
{
  "Id": null,
  "Method": "AdjustBalance",
  "Params": {
    "flake_id": 9148077483121591870,
    "real_amount": 1.00,
    "bonus_amount": 1.00,
    "ring_fenced_amount": 1.00,
    "total_amount": 3.00,
    "bonus_game_type": "G",
    "user_id": 1030979,
    "cmscore_game_id": 2885,
    "regulated_game_id": 6403,
    "transaction_type_id": 2,
    "partner_transaction_id": "04/08/2021 Jason 1",
    "partner_timestamp_utc": "2021-08-04T10:42:44.393Z",
    "partner_id": 100,
    "game_round_id": 1,
    "product_id": 19,
    "token": "99faa686-3189-4eea-b789-bf828ed78a34",
    "action_type_id": 1,
    "provider_region_id": 20,
    "allow_bonus": true,
    "allow_ring_fenced": true,
    "currency_id": 1,
    "bet365_transaction_id": 5000390220,
    "user_bonus_id": 1234,
    "external_balance_change": {
      "bonus_amount": 1.00,
      "ring_fenced_amount": 1.00
    }
  }
}
```

| parameter | type | description |
|----|----|----|
|flake_id|int64|An int64 number generated through our distributed ID generation library|
|real_amount|string or decimal (e.g `"-3.50"` or `-3.50`)|The real funds staked/returned
|bonus_amount|string or decimal (e.g `"-3.50"` or `-3.50`)|The bonus funds staked/returned
|ring_fenced_amount|string or decimal (e.g `"-3.50"` or `-3.50`)|The ring fenced funds staked/returned
|total_amount|string or decimal (e.g `"-3.50"` or `-3.50`)|The total amount is the sum of all balance changes including `external_balance_change`
|bonus_game_type|string|The bonus game type of the game used in the transaction|
|user_id|int32|The user id of the user|
|cmscore_game_id|int32|The CMS Core game ID|
|regulated_game_id|int32|The regulated game ID|
|transaction_type_id|int32|The transaction type ID. Behaviour depends on this see [Parameters for each transaction type](#parameters-for-each-transaction-type)|
|partner_transaction_id|string|The partners transaction ID|
|partner_timestamp_utc|string|The partner timestamp in UTC|
|partner_id|int32|The partner ID of the partner who triggered this transaction|
|game_round_id|int64|The Game Round ID that this transaction belongs to|
|product_id|int32|The ProductID|
|token|string|The private token that was used|
|action_type_id|int32|The action type ID|
|provider_region_id|int32|The provider region ID|
|allow_bonus|bool|Whether to allow the use of bonus funds in this transaction|
|allow_ring_fenced|bool|Whether to allow the user of ring fenced funds in this transaction
|currency_id|int32|The currency ID of the user|
|bet365_transaction_id|int64|The transaction ID returned from GBM
|user_bonus_id|int32|The user's GBT bonus ID
|external_balance_change|object|Contains balance changes external to us (e.g IMS bonuses)
|external_balance_change -> bonus_amount|string or decimal (e.g `"-3.50"` or `-3.50`)|The bonus amount changed externally
|external_balance_change -> ring_fenced_amount|string or decimal (e.g `"-3.50"` or `-3.50`)|The ring fenced amount changed externally


#### Parameters for each transaction type

| Transaction type                                                                                                                                                                                                                                                                                                             | Parameters                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stake(2)<br/>TransferToBingoBonusBalance(47)<br/>GamingStake(133)<br/>FreeSpinStake(137)<br/>GoldenChipStake(141)<br/>TransferToPlaytechBonusCasino(149)<br/>PokerBuyChips(161)<br/>PokerTournamentEntry(165)<br/>TransferToPlaytechBonusPoker(169)<br/>PokerTournamentRebuy(207)<br/>TransferToPlaytechBonusLiveCasino(227) | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `cmscore_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `bonus_game_type`, `allow_bonus`, `allow_ring_fenced`, `bet365_transaction_id`, `product_id`, `currency_id`<br />For a stake which has external balance changes you should add `external_balance_change`<br />For a stake which spends GBT bonus you should add `user_bonus_id`                                                 |
| InstantGamesStake(82)                                                                                                                                                                                                                                                                                                        | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `cmscore_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `bonus_game_type`, `allow_bonus`, `allow_ring_fenced`, `bet365_transaction_id`, `product_id`, `currency_id`                                                                                                                                                                                                                     |
| Return(3)<br/>TransferFromBingoBonusBalance(44)<br/>GamingReturn(134)<br/>FreeSpinReturn(138)<br/>GoldenChipReturn(142)<br/>TransferFromPlaytechBonusCasino(151)<br/>PokerSellChips(163)<br/>PokerTournamentWin(167)<br/>TransferFromPlaytechBonusPoker(171)<br/>TransferFromPlaytechBonusLiveCasino(229)                    | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `real_amount`, `ring_fenced_amount`, `bonus_amount`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `source_bet365_games_transaction_id`, `allow_bonus`, `allow_ring_fenced`, `product_id`, `currency_id`, `external_balance_change`<br />For a return which has external balance changes you should add `external_balance_change`<br />For a return for GBT bonus you should add `user_bonus_id` | 
| InstantGamesReturn(84)                                                                                                                                                                                                                                                                                                       | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `real_amount`, `ring_fenced_amount`, `bonus_amount`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `bonus_game_type`, `source_bet365_games_transaction_id`, `allow_bonus`, `allow_ring_fenced`, `product_id`, `currency_id`                                                                                                                                                                      |
| GamingPrizeAward(199)<br/>CashDropAward(243)<br/>TournamentAward(245)                                                                                                                                                                                                                                                        | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `real_amount`, `ring_fenced_amount`, `bonus_amount`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `bonus_game_type`, `source_bet365_games_transaction_id`, `allow_bonus`, `allow_ring_fenced`, `product_id`, `currency_id`                                                                                                                                                                      |
| VoidStake(4)<br/>VoidGamingStake(135)<br/>VoidPokerBuyChips(162)<br/>VoidPokerTournamentEntry(166), VoidPokerTournamentRebuy(208)                                                                                                                                                                                            | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `real_amount`, `ring_fenced_amount`, `bonus_amount`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `bonus_game_type`, `source_bet365_games_transaction_id`, `product_id`, `currency_id`<br />For a void stake which has external balance changes you should add `external_balance_change`<br />For a void stake for GBT bonus you should add `user_bonus_id`                                     |
| VoidInstantGamesStake(83)                                                                                                                                                                                                                                                                                                    | `flake_id`, `transaction_type_id`, `game_round_id`, `partner_transaction_id`, `real_amount`, `ring_fenced_amount`, `bonus_amount`, `total_amount`, `partner_timestamp_utc`, `user_id`, `regulated_game_id`, `partner_id`, `provider_region_id`, `token`, `action_type_id`, `bonus_game_type`, `source_bet365_games_transaction_id`, `product_id`, `currency_id`                                                                                                                                                                                                          |


#### Response

See [Transaction insert response](#transaction-insert-response)

### POST /api/QueueManualVoidReturn

    Automatically voiding a return is not allowed. So this endpoint just inserts an entry into a table. 

#### Request

```json
{
    "Id": null,
    "Method": "QueueManualVoidReturn",
    "Params": {
    "transaction_type_id": 6,
    "source_bet365_games_transaction_id": 2156886653
    }
}
```

| parameter | type | description |
|----|----|----|
|transaction_type_id|int32|The transaction type to record
|source_bet365_games_transaction_id|int64|The source transaction ID to void

### POST /api/GetTransactionsByGameRound

    Retrieves all transactions in the gameround

#### Request

```json
{
    "Id": null,
    "Method": "GetTransactionsByGameround",
    "Params": {
    "game_round_id": 3
    }
}
```

| parameter | type | description |
|----|----|----|
|game_round_id|int64|The game round ID

#### Response

An array of [Transaction select responses](#transaction-select-response)

```json
{
  "id": null,
  "result": [
    {
      "bet365_games_transaction_id": 2200354208,
      "transaction_type_id": 129,
      "action_type_id": 1,
      "partner_transaction_id": "EXTRA12347",
      "real_amount": "-1.2",
      "ring_fenced_amount": "0",
      "bonus_amount": "0",
      "total_amount": "-1.2",
      "total_amount_gbp": "-0.9382",
      "currency_code": "USD",
      "partner_timestamp_utc": "2020-09-30T11:00:00Z",
      "game_round_id": 3,
      "bet365_transaction_id": 0,
      "provider_region_id": 0,
      "source_transaction_id": 0,
      "regulated_game_id": 12345,
      "user_token": "41b9cd97-5130-4431-89b9-54a7d1ba7d0f",
      "flake_id": 1602579243275560455,
      "is_new": false
    }
  ]
}
```

### POST /api/GetTransactionByID

    Retrieves a transaction by its Transaction ID

#### Request 

```json
{
    "Id": null,
    "Method": "GetTransactionById",
    "Params": {
    "id": 2202509630
    }
}
```

| parameter | type | description |
|----|----|----|
|id|int64|The transaction ID

#### Response

See [Transaction select response](#transaction-select-response)

### POST /api/GetTransactionsByPartnerTransactionID

    Retrieves a transaction by its partner/external transaction ID

#### Request

```json
{
    "Id": null,
    "Method": "GetTransactionsByPartnerTransactionID",
    "Params": {
        "user_id": 100058196,
        "provider_region_id": 3,
        "partner_transaction_id": "498/02-08-2021-Jason-1-1"
    }
}
```

| parameter | type | description |
|----|----|----|
|user_id|int32|The user ID
|provider_region_id|int32|The provider region ID
|partner_transaction_id|string|The partner transaction ID

#### Response

An array of [Transaction select responses](#transaction-select-response)

```json
{
    "id": null,
    "result": [
        {
            "bet365_games_transaction_id": 2202486381,
            "transaction_type_id": 133,
            "action_type_id": 0,
            "partner_transaction_id": "498/02-08-2021-Jason-1-1",
            "real_amount": "-1.5",
            "ring_fenced_amount": "0",
            "bonus_amount": "0",
            "total_amount": "-1.5",
            "total_amount_gbp": "-1.5",
            "currency_code": "GBP",
            "partner_timestamp_utc": "2021-08-02T16:30:49Z",
            "game_round_id": 643,
            "bet365_transaction_id": 0,
            "provider_region_id": 3,
            "source_transaction_id": 0,
            "regulated_game_id": 90025,
            "user_token": "9CCE522A-2D82-4579-9B73-83C5F41E0960",
            "flake_id": 9148077482796571274,
            "is_new": false
        }
    ]
}
```

### POST /api/GetLatestTransactionByTransactionTypeId

    Gets the latest transaction for a user by its transaction ID

#### Request

```json
{
    "Id": null,
    "Method": "GetLatestTransactionByTransactionTypeId",
    "Params": {
        "user_id": 1030979,
        "provider_region_id": 3,
        "partner_id": 28,
        "transaction_type_id": 2
    }
}
```

| parameter | type | description |
|----|----|----|
|user_id|int32|The user ID
|provider_region_id|int32|The provider region ID
|partner_id|int32|The partner ID
|transaction_type_id|int32|The transaction type ID

#### Response

See [Transaction select response](#transaction-select-response)