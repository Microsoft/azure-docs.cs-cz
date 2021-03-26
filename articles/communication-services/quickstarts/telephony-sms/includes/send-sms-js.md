---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: b0a173d605da859830e288aebf355117b928090a
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110332"
---
Začínáme s komunikačními službami Azure pomocí komunikačních služeb JavaScript SDK pro komunikaci, které vám umožní posílat zprávy SMS.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo s povoleným SMS. [Získejte telefonní číslo](../get-phone-number.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V terminálu nebo příkazovém okně spusťte příkaz `node --version` a ověřte, zda je nainstalován Node.js.
- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Nejdřív otevřete okno terminálu nebo příkaz, vytvořte pro svoji aplikaci nový adresář a přejděte do něj.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

Pomocí textového editoru vytvořte soubor s názvem **send-sms.js** v kořenovém adresáři projektu. Do tohoto souboru přidáte veškerý zdrojový kód pro tento rychlý Start v následujících oddílech.

### <a name="install-the-package"></a>Instalace balíčku

Pomocí `npm install` příkazu nainstalujte sadu Azure Communications Services SMS SDK pro JavaScript.

```console
npm install @azure/communication-sms --save
```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady SMS SDK služby Azure Communications Services pro Node.js.

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Tato třída je potřebná pro všechny funkce SMS. Vytvoří se jeho instance s informacemi o předplatném a použije se k posílání zpráv SMS. |
| SmsSendResult               | Tato třída obsahuje výsledek ze služby SMS.                                          |
| SmsSendOptions | Toto rozhraní poskytuje možnosti konfigurace vytváření sestav o doručení. Pokud `enableDeliveryReport` je nastaveno na `true` , pak dojde k vygenerování události po úspěšném doručení. |
| SmsSendRequest | Toto rozhraní je modelem vytváření žádosti serveru SMS (např. Nakonfigurujte telefonní čísla na a z a obsah serveru SMS. |

## <a name="authenticate-the-client"></a>Ověření klienta

Importujte **SmsClient** ze sady SDK a vytvořte její instanci pomocí připojovacího řetězce. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).

Přidejte následující kód pro **send-sms.js**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Odeslat zprávu o 1: N SMS

Chcete-li odeslat zprávu SMS seznamu příjemců, zavolejte `send` funkci z SmsClient se seznamem příjemců s telefonními čísly (Pokud chcete poslat zprávu jednomu příjemci, zahrňte pouze jedno číslo v seznamu). Přidejte tento kód na konec **send-sms.js**:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Měli byste nahradit `<from-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým k vašemu prostředku komunikačních služeb a `<to-phone-number>` telefonním číslem, na které chcete poslat zprávu.

## <a name="send-a-1n-sms-message-with-options"></a>Odeslat zprávu o 1: N SMS s možnostmi

K určení, zda má být povolena zpráva o doručení a nastavena vlastní značky, můžete také předat objekt Options.

```javascript

async function main() {
  await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameter
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

`enableDeliveryReport`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .
`tag` je volitelný parametr, který můžete použít k použití značky pro sestavu doručení.

## <a name="run-the-code"></a>Spuštění kódu

Pomocí `node` příkazu spusťte kód, který jste přidali do souboru **send-sms.js** .

```console

node ./send-sms.js

```
