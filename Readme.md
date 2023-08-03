
Only the 6th section - Fabric 2.x - First smart contract is having the relevant code at the time of writing this document (Aug 2, 2023)

They are using Hyperledger Composer which is depreciated since 2019. But it gives a clear idea on how to setup a hyperledger fabric.

# Section 3: Fun in the playground:

Here I will be explaing the steps involved in setting up the very basic things needed for running Hyperledger Composer. Hyperledger Composer does the heavy lifting for us regarding downloading the required docker containers. We just need to specify the code that will be used in the Hyperledger fabric.

## 1. Models

They are defining the models. You can think of models are essentially data that we want to add to the blockchain (The video clearly explains how hash and block chain works). They are creating 3 basic things called `asset`, `participant`, and `transaction` as shown below. In this example they have a `Commodity` that can traded between two traders. `Trade` is a transaction.

```
/**
 * My commodity trading network
 */
namespace org.example.biznet
asset Commodity identified by tradingSymbol {
    o String tradingSymbol
    o String description
    o String mainExchange
    o Double quantity
    --> Trader owner
}
participant Trader identified by tradeId {
    o String tradeID
    o String firstName
    o String lastName
}
transaction Trade {
    --> Commodity commodity
    --> Trader newOwner
}

```

## 2. Business logic

- They have written some code that handles the business logic:

```
/**
 * Track the trade of a commodity from one trader to another
 * @param {org.acme.mynetwork.Trade} trade - the trade to be processed
 * @transaction
 */

async function tradeCommodity(trade) {
    trade.commodity.owner = trade.newOwner;
    let assetRegistry = await getAssetRegistry('org.acme.mynetwork.Commodity');
    await assetRegistry.update(trade.commodity);
}
```
- Here the commented code (@param & @transaction) are used by the hyperledger. So they are very important.

# Section 4: 

## 4. Permissions

- Every transcation that we do to our hyperledger is done through this access control policy which tells who controls what.
- The 1st policy will give everyone access to all the resources.
- The 2nd policy will give access to everyone in the composer environment

```
/**
 * Access control rules for tutorial-network
 */
rule Default {
    description: "Allow all participants access to all resources"
    participant: "ANY"
    operation: ALL
    resource: "org.example.biznet.*"
    action: ALLOW
}

rule SystemACL {
  description:  "System ACL to permit all access"
  participant: "ANY"
  operation: ALL
  resource: "org.hyperledger.composer.system.**"
  action: ALLOW
}

```

Using all of the above we can setup our business network