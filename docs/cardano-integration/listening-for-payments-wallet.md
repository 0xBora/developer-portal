---
id: listening-for-payments-wallet
title: Listening for ADA payments using cardano-wallet
sidebar_label: Receiving payments (cardano-wallet)
description: How to listen for ADA Payments with the cardano-wallet
--- 
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

### Overview 
:::note
This guide assumes that you have basic understanding of `cardano-wallet`, how to use it and that you have installed it into your system. Otherwise we recommend reading [Installing cardano-node](/docs/cardano-integration/installing-cardano-node), [Running cardano-node](/docs/cardano-integration/running-cardano) and [Exploring Cardano Wallets](/docs/cardano-integration/creating-wallet-faucet) guides first.

This guide also assumed that you have `cardano-node` and `cardano-wallet` running in the background and connected to the `testnet` network.
:::

### Use case
There are many possible reasons why you would want to have the functionality of listening for `ADA` payments, but a very obvious use case would be for something like an **online shop** or a **payment gateway** that uses `ADA` tokens as the currency.

![img](../../static/img/cardano-integrations/ada-online-shop.png)

### Technical Flow
To understand how something like this could work in a technical point of view, let's take a look at the following diagram:

![img](../../static/img/cardano-integrations/ada-payment-flow-wallet.png)

So let's imagine a very basic scenario where a **customer** is browsing an online shop. Once the user has choosen and added all the items into the **shopping cart**. The next step would then be to checkout and pay for the items, Of course we will be using **Cardano** for that!

The **front-end** application would then request for a **wallet address** from the backend service and render a QR code to the **customer** to be scanned via a **Cardano wallet**. The backend service would then know that it has to query the `cardano-wallet` with a certain time interval to confirm and alert the **front-end** application that the payment has completed succesfully.

In the meantime the transaction is then being processed and settled within the **Cardano** network. We can see in the diagram above that both parties are ultimately connected to the network via the `cardano-node` software component.

### Time to code!

Now let's get our hands dirty and see how we can implement something like this in actual code.

:::note
In this section, We will use the path `/home/user/receive-ada-sample` to store all the related files as an example, please replace it with the directory you have choosen to store the files. 
All the code examples in this article assumes that you will save all the source-code-files under the root of this directory.
:::

**Generate Wallet and Request some tADA**

First, we create our new **wallet** via `cardano-wallet` **REST API**:

** Generate Seed ** 

<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>


  <TabItem value="js">

```csharp
using System;
using System.IO;
using System.Linq;

// Install using command `dotnet add package SimpleExec --version 7.0.0`
using SimpleExec;

var mnemonicSeed = await Command.ReadAsync("cardano-wallet", "recovery-phrase generate", noEcho: true);
Console.WriteLine(mnemonicSeed);
```

  </TabItem>

  <TabItem value="py">

```csharp
using System;
using System.IO;
using System.Linq;

// Install using command `dotnet add package SimpleExec --version 7.0.0`
using SimpleExec;

var mnemonicSeed = await Command.ReadAsync("cardano-wallet", "recovery-phrase generate", noEcho: true);
Console.WriteLine(mnemonicSeed);
```

  </TabItem>

  <TabItem value="cs">

```csharp
using System;
using System.IO;
using System.Linq;

// Install using command `dotnet add package SimpleExec --version 7.0.0`
using SimpleExec;

var mnemonicSeed = await Command.ReadAsync("cardano-wallet", "recovery-phrase generate", noEcho: true);
Console.WriteLine(mnemonicSeed);
```

  </TabItem>

  <TabItem value="ts">

```csharp
using System;
using System.IO;
using System.Linq;

// Install using command `dotnet add package SimpleExec --version 7.0.0`
using SimpleExec;

var mnemonicSeed = await Command.ReadAsync("cardano-wallet", "recovery-phrase generate", noEcho: true);
Console.WriteLine(mnemonicSeed);
```

  </TabItem>
</Tabs>

** Restore wallet from seed ** 

<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>


  <TabItem value="js">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

var hc = new HttpClient();

var payload = new StringContent(JsonSerializer.Serialize(new
{
    name = "test_cf_1",
    mnemonic_sentence = new[] { "expose", "biology", "will", "pause", "taxi", "behave", "inquiry", "lock", "matter", "pride", "divorce", "model", "little", "easily", "solid", "need", "dose", "sadness", "kitchen", "pyramid", "erosion", "shoulder", "double", "fragile" },
    passphrase = "test123456"
}), Encoding.UTF8, "application/json");

var resp = await hc.PostAsync("http://localhost:9998/v2/wallets", payload);
```

  </TabItem>

  <TabItem value="ts">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

var hc = new HttpClient();

var payload = new StringContent(JsonSerializer.Serialize(new
{
    name = "test_cf_1",
    mnemonic_sentence = new[] { "expose", "biology", "will", "pause", "taxi", "behave", "inquiry", "lock", "matter", "pride", "divorce", "model", "little", "easily", "solid", "need", "dose", "sadness", "kitchen", "pyramid", "erosion", "shoulder", "double", "fragile" },
    passphrase = "test123456"
}), Encoding.UTF8, "application/json");

// Assuming cardano-wallet is listening on port 9998
var resp = await hc.PostAsync("http://localhost:9998/v2/wallets", payload);
```

  </TabItem>

  <TabItem value="py">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

var hc = new HttpClient();

var payload = new StringContent(JsonSerializer.Serialize(new
{
    name = "test_cf_1",
    mnemonic_sentence = new[] { "expose", "biology", "will", "pause", "taxi", "behave", "inquiry", "lock", "matter", "pride", "divorce", "model", "little", "easily", "solid", "need", "dose", "sadness", "kitchen", "pyramid", "erosion", "shoulder", "double", "fragile" },
    passphrase = "test123456"
}), Encoding.UTF8, "application/json");

// Assuming cardano-wallet is listening on port 9998
var resp = await hc.PostAsync("http://localhost:9998/v2/wallets", payload);
```

  </TabItem>

  <TabItem value="cs">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();

var payload = new StringContent(JsonSerializer.Serialize(new
{
    name = "test_cf_1",
    mnemonic_sentence = new[] { "expose", "biology", "will", "pause", "taxi", "behave", "inquiry", "lock", "matter", "pride", "divorce", "model", "little", "easily", "solid", "need", "dose", "sadness", "kitchen", "pyramid", "erosion", "shoulder", "double", "fragile" },
    passphrase = "test123456"
}), Encoding.UTF8, "application/json");

// Restore the wallet using the previously generated seed. Assuming cardano-wallet is listening on port 9998
var resp = await hc.PostAsync("http://localhost:9998/v2/wallets", payload);
```

  </TabItem>

</Tabs>

** Get a unused wallet address to receive some payments **

We will get a **wallet address** to show to the customers and for them to send payments to the wallet. In this case we can use the address to request some `tADA` from the **Testnet Faucet** and simulate a payment:


<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>


  <TabItem value="js">

```csharp
// Retrieve wallet address from previously created wallet
// Replace with the wallet Id you previously generated above
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
var getAddressResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}/addresses?state=unused");
var jsonString = await getAddressResp.Content.ReadAsStringAsync();
var addressResponse = JsonSerializer.Deserialize<JsonElement>(jsonString);
var firstWalletAddress = addressResponse[0].GetProperty("id");
```

  </TabItem>

  <TabItem value="ts">

```csharp
// Retrieve wallet address from previously created wallet
// Replace with the wallet Id you previously generated above
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
var getAddressResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}/addresses?state=unused");
var jsonString = await getAddressResp.Content.ReadAsStringAsync();
var addressResponse = JsonSerializer.Deserialize<JsonElement>(jsonString);
var firstWalletAddress = addressResponse[0].GetProperty("id");
```

  </TabItem>

  <TabItem value="py">

```csharp
// Retrieve wallet address from previously created wallet
// Replace with the wallet Id you previously generated above
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
var getAddressResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}/addresses?state=unused");
var jsonString = await getAddressResp.Content.ReadAsStringAsync();
var addressResponse = JsonSerializer.Deserialize<JsonElement>(jsonString);
var firstWalletAddress = addressResponse[0].GetProperty("id");
```

  </TabItem>

  <TabItem value="cs">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();
// Retrieve wallet address from previously created wallet
// Replace with the wallet Id you previously generated above
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
var getAddressResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}/addresses?state=unused");
var jsonString = await getAddressResp.Content.ReadAsStringAsync();
var addressResponse = JsonSerializer.Deserialize<JsonElement>(jsonString);
var firstWalletAddress = addressResponse[0].GetProperty("id");
```

  </TabItem>

</Tabs>

** Retrieve wallet balance **

We will then retrieve the wallet details to get stuff like its `sync status`, `native assets` and `balance (lovelace)`. We can then use the `balance` to check if we have received a some payment.

<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>


  <TabItem value="js">

```csharp
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();
```

  </TabItem>

  <TabItem value="ts">

```csharp
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();
```

  </TabItem>

  <TabItem value="py">

```csharp
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();
```

  </TabItem>

  <TabItem value="cs">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();
```

  </TabItem>

</Tabs>

### Running and Testing

Our final code should look something like this:

<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>


  <TabItem value="js">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();

// Check if payment is complete
var isPaymentComplete = balance >= totalExpectedLovelace;

Console.WriteLine($"Total Received: {balance} LOVELACE");
Console.WriteLine($"Expected Payment: {totalExpectedLovelace} LOVELACE");
Console.WriteLine($"Payment Complete: {(isPaymentComplete ? "✅":"❌")}");
```

  </TabItem>

  <TabItem value="ts">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();

// Check if payment is complete
var isPaymentComplete = balance >= totalExpectedLovelace;

Console.WriteLine($"Total Received: {balance} LOVELACE");
Console.WriteLine($"Expected Payment: {totalExpectedLovelace} LOVELACE");
Console.WriteLine($"Payment Complete: {(isPaymentComplete ? "✅":"❌")}");
```

  </TabItem>

  <TabItem value="py">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();

// Check if payment is complete
var isPaymentComplete = balance >= totalExpectedLovelace;

Console.WriteLine($"Total Received: {balance} LOVELACE");
Console.WriteLine($"Expected Payment: {totalExpectedLovelace} LOVELACE");
Console.WriteLine($"Payment Complete: {(isPaymentComplete ? "✅":"❌")}");
```

  </TabItem>

  <TabItem value="cs">

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;

using var hc = new HttpClient();
// Get Wallet Details / Balance
// Replace with your wallet Id
var walletId = "101b3814d6977de4b58c9dedc67b87c63a4f36dd";
// The total payment we expect in lovelace unit
var totalExpectedLovelace = 1000000;
var getWalletResp = await hc.GetAsync($"http://localhost:9998/v2/wallets/{walletId}");
var jsonString = await getWalletResp.Content.ReadAsStringAsync();
var walletResp = JsonSerializer.Deserialize<JsonElement>(jsonString);
var balance = walletResp.GetProperty("balance").GetProperty("total").GetProperty("quantity").GetInt32();

// Check if payment is complete
var isPaymentComplete = balance >= totalExpectedLovelace;

Console.WriteLine($"Total Received: {balance} LOVELACE");
Console.WriteLine($"Expected Payment: {totalExpectedLovelace} LOVELACE");
Console.WriteLine($"Payment Complete: {(isPaymentComplete ? "✅":"❌")}");
```

  </TabItem>

</Tabs>


Now we are ready to test 🚀, running the code should give us the following result:

<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>
  <TabItem value="js">

```bash
❯ node checkPayment.js
Total Received: 0 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ❌
```

  </TabItem>
  <TabItem value="ts">


```bash
❯ ts-node checkPayment.ts
Total Received: 0 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ❌
```

  </TabItem>
  <TabItem value="cs">

```bash
❯ dotnet run
Total Received: 0 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ❌
```

  </TabItem>
  <TabItem value="py">

```bash
❯ python checkPayment.py 
Total Received: 0 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ❌
```

  </TabItem>
</Tabs>

The code is telling us that our current wallet has received a total of `0 lovelace` and it expected `1,000,000 lovelace`, therefore it concluded that the payment is not complete.

### Complete the payment

What we can do to simulate a succesful payment is to send atleast `1,000,000 lovelace` into the **wallet address** that we have just generated for this project. We show how you can get the **wallet address** via code in the examples above.

Now simply send atleast `1,000,000 lovelace` to this **wallet address** or request some `test ADA` funds from the **Testnet Faucet**. Once complete, we can now run the code again and we should see a succesful result this time.

<Tabs
  defaultValue="js"
  groupId="language"
  values={[
    {label: 'JavaScript', value: 'js'},
    {label: 'Typescript', value: 'ts'},
    {label: 'Python', value: 'py'},
    {label: 'C#', value: 'cs'}
  ]}>
  <TabItem value="js">

```bash
❯ node checkPayment.js
Total Received: 1000000000 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ✅
```

  </TabItem>
  <TabItem value="ts">


```bash
❯ ts-node checkPayment.ts
Total Received: 1000000000 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ✅
```

  </TabItem>
  <TabItem value="cs">

```bash
❯ dotnet run
Total Received: 1000000000 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ✅
```

  </TabItem>
  <TabItem value="py">

```py
❯ python checkPayment.py 
Total Received: 1000000000 LOVELACE
Expected Payment: 1000000 LOVELACE
Payment Complete: ✅
```

  </TabItem>
</Tabs>

:::note
It might take 20 seconds or more for the transaction to propagate within the network depending on the network health, so you will have to be patient.
:::


Congratulations, we are now able to detect succesful **Cardano** payments programatically. This should help you bring integrations to your existing or new upcoming applications. 🎉🎉🎉