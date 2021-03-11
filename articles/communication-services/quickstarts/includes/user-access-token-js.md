---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 2726be7b137b511795d92c62174158f33108cad9
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623355"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte klientskou knihovnu pro službu Azure Communications identity pro JavaScript.

```console

npm install @azure/communication-identity --save

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

## <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít nový textový soubor v editoru kódu
1. Přidejte `require` volání pro načtení `CommunicationIdentityClient`
1. Vytvoření struktury pro program, včetně základního zpracování výjimek

Pro začátek použijte následující kód:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. Uložte nový soubor jako **issue-access-token.js** v adresáři *Access-tokens-rychlý Start* .

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `CommunicationIdentityClient` s připojovacím řetězcem. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Do metody `main` přidejte následující kód:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

Alternativně můžete oddělit koncový bod a přístupový klíč.
```javascript
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const accessKey = process.env["COMMUNICATION_SERVICES_ACCESSKEY"];
const tokenCredential = new AzureKeyCredential(accessKey);
// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential)
```

Pokud jste nastavili spravovanou identitu, přečtěte si téma [použití spravovaných identit](../managed-identity.md). můžete se taky ověřit pomocí spravované identity.
```javascript
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Vytvoření identity

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Uložte si získanou identitu s mapováním na uživatele vaší aplikace. Například uložením do databáze aplikačního serveru. Identita se vyžaduje později pro vydávání přístupových tokenů.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Vystavení přístupových tokenů

Použijte `getToken` metodu pro vydání přístupového tokenu pro již existující identitu komunikačních služeb. Parametr `scopes` definuje sadu primitivních hodnot, které budou autorizovat tento přístupový token. Podívejte se na [seznam podporovaných akcí](../../concepts/authentication.md). Nová instance parametru `communicationUser` se dá sestavit na základě řetězcové reprezentace identity komunikační služby Azure.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Přístupové tokeny jsou krátkodobé přihlašovací údaje, které je potřeba znovu vydat. V takovém případě může dojít k přerušení činnosti uživatelů vaší aplikace. `expiresOn`Vlastnost Response označuje dobu života přístupového tokenu.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Vytvoření identity a vydání přístupového tokenu v rámci stejné žádosti

Použijte `createUserAndToken` metodu k vytvoření identity komunikačních služeb a vydání přístupového tokenu pro něj. Parametr `scopes` definuje sadu primitivních hodnot, které budou autorizovat tento přístupový token. Podívejte se na [seznam podporovaných akcí](../../concepts/authentication.md).

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await this.client.createUserAndToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>Obnovení přístupových tokenů

Aktualizace přístupových tokenů je stejně snadné jako volání `getToken` se stejnou identitou, která byla použita k vystavování tokenů. Je také nutné zadat `scopes` aktualizované tokeny.

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Odvolat přístupové tokeny

V některých případech je možné explicitně odvolat přístupové tokeny. Například když uživatel aplikace změní heslo, které používá k ověření vaší služby. Metoda `revokeTokens` zrušila platnost všech aktivních přístupových tokenů, které byly vystaveny identitě.

```javascript
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Odstranění identity

Odstranění identity odvolá všechny aktivní přístupové tokeny a zabrání vám v vystavování přístupových tokenů pro danou identitu. Zároveň se tím odebere veškerý trvalý obsah přidružený k identitě.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Spuštění kódu

V příkazovém řádku konzoly přejděte do adresáře obsahujícího soubor *issue-access-token.js* a pak `node` Spusťte následující příkaz, který aplikaci spustí.

```console
node ./issue-access-token.js
```
