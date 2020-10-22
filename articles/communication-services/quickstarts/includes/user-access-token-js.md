---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 22cfe369561eab1ca334c7ff2450162dfae3e761
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347079"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte klientskou knihovnu pro správu služby Azure Communication Services pro JavaScript.

```console

npm install @azure/communication-administration --save

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

## <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít nový textový soubor v editoru kódu
1. Přidejte `require` volání pro načtení `CommunicationIdentityClient`
1. Vytvoření struktury pro program, včetně základního zpracování výjimek

Pro začátek použijte následující kód:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Uložte nový soubor jako **issue-token.js** v adresáři *User-tokens-Starter* .

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `CommunicationIdentityClient` s připojovacím řetězcem. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Do metody `main` přidejte následující kód:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Vytvoření uživatele

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Měli byste udržovat mapování mezi uživateli vaší aplikace a komunikačními službami, které vygenerovaly identity (například jejich uložením do databáze aplikačního serveru).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Vystavení přístupových tokenů uživatele

Použijte `issueToken` metodu pro vydání přístupového tokenu pro uživatele komunikačních služeb. Pokud nezadáte volitelný parametr, vytvoří se `user` Nový uživatel, který se vrátí s tokenem.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Tokeny přístupu uživatele jsou krátkodobé přihlašovací údaje, které je potřeba znovu vystavit, aby nedocházelo k přerušení služeb uživatelů. `expiresOn`Vlastnost Response označuje dobu života tokenu.

## <a name="revoke-user-access-tokens"></a>Odvolat tokeny přístupu uživatele

V některých případech může být nutné explicitně odvolat tokeny přístupu uživatele, například když uživatel změní heslo, které používá k ověření vaší služby. Tato metoda používá `revokeTokens` k devalidaci všech přístupových tokenů uživatele.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="refresh-user-access-tokens"></a>Aktualizovat tokeny přístupu uživatele

Chcete-li aktualizovat token, použijte `CommunicationUser` objekt k opakovanému vystavení:

```javascript  
let userResponse = new CommunicationUser(existingUserId);
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
```

## <a name="delete-a-user"></a>Odstranění uživatele

Odstranění uživatele odvolá všechny aktivní tokeny a zabrání vám v vydávání následných tokenů pro identity. Zároveň se tím odebere veškerý trvalý obsah přidružený k uživateli.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Spuštění kódu

V příkazovém řádku konzoly přejděte do adresáře obsahujícího soubor *issue-token.js* a pak `node` Spusťte následující příkaz, který aplikaci spustí.

```console
node ./issue-token.js
```
