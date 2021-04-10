---
ms.openlocfilehash: 23c64cc91378be605481eb554af2178100df3508
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629368"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V terminálu nebo příkazovém okně spusťte příkaz `node --version` a ověřte, zda je nainstalován Node.js.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Nejdřív otevřete okno terminálu nebo příkaz, vytvořte pro svoji aplikaci nový adresář a přejděte do něj.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

V kořenovém adresáři adresáře, který jste právě vytvořili, vytvořte soubor s názvem **phone-numbers-quickstart.js** . Přidejte do něj následující fragment kódu:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte klientskou knihovnu telefonních čísel služby Azure Communication Services pro JavaScript.

```console
npm install @azure/communication-phone-numbers --save
```

`--save`Možnost přidá knihovnu do **package.js** v souboru jako závislost.

## <a name="authenticate-the-client"></a>Ověření klienta

Naimportujte **PhoneNumbersClient** z klientské knihovny a vytvořte její instanci pomocí připojovacího řetězce. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).

Do horní části **phone-numbers-quickstart.js** přidejte následující kód:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Správa telefonních čísel

### <a name="search-for-available-phone-numbers"></a>Vyhledat dostupná telefonní čísla

Aby bylo možné koupit telefonní čísla, je třeba nejprve vyhledat dostupná telefonní čísla. Chcete-li vyhledat telefonní čísla, zadejte směrové číslo oblasti, typ přiřazení, [Možnosti telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [typ telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)a množství. Všimněte si, že pro typ telefonního čísla bez poplatků je zadání kódu oblasti volitelné.

Do funkce přidejte následující fragment kódu `main` :

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Koupit telefonní číslo

Výsledek hledání telefonních čísel je `PhoneNumberSearchResult` . Obsahuje, `searchId` který se dá předávat do rozhraní API nákupních čísel, aby se získaly čísla ve vyhledávání. Všimněte si, že volání rozhraní API pro telefonní čísla nákupu bude mít za následek poplatek za váš účet Azure.

Do funkce přidejte následující fragment kódu `main` :

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Aktualizace možností telefonního čísla

Po zakoupení telefonního čísla přidejte následující kód, který aktualizuje své možnosti:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Koupit telefonní čísla

Po nákupním čísle ho můžete načíst z klienta. Do funkce přidejte následující kód `main` , který získá telefonní číslo, které jste právě koupili:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Můžete také načíst všechna zakoupená telefonní čísla.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Telefonní číslo verze

Nyní si můžete koupit vydané telefonní číslo. Do funkce přidejte následující fragment kódu `main` :

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Spuštění kódu

Pomocí `node` příkazu spusťte kód, který jste přidali do souboru **phone-numbers-quickstart.js** .

```console
node phone-numbers-quickstart.js
```