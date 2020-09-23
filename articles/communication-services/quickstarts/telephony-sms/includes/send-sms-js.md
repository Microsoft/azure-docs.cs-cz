---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: cdd4988f9a23904c0771852c4539aa9bce2ee683
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947027"
---
Začínáme s komunikačními službami Azure pomocí klientské knihovny služby Communications JavaScript pro službu SMS pro posílání zpráv SMS.

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

Pomocí `npm install` příkazu nainstalujte klientskou knihovnu služby Azure Communications Services pro JavaScript.

```console
npm install @azure/communication-sms --save
```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny SMS služby Azure Communications Services pro Node.js.

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Tato třída je potřebná pro všechny funkce SMS. Vytvoří se jeho instance s informacemi o předplatném a použije se k posílání zpráv SMS. |
| SendSmsOptions | Toto rozhraní poskytuje možnosti konfigurace vytváření sestav o doručení. Pokud `enable_delivery_report` je nastaveno na `true` , pak dojde k vygenerování události po úspěšném doručení. |
| SendMessageRequest | Toto rozhraní je modelem vytváření žádosti serveru SMS (např. Nakonfigurujte telefonní čísla na a z a obsah serveru SMS. |

## <a name="authenticate-the-client"></a>Ověření klienta

Naimportujte **SmsClient** z klientské knihovny a vytvořte její instanci pomocí připojovacího řetězce. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).

Přidejte následující kód pro **send-sms.js**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Odeslání zprávy SMS

Odešle zprávu SMS voláním `send` metody. Přidejte tento kód na konec **send-sms.js**:

```javascript
await smsClient.send({
  from: "<leased-phone-number>",
  to: ["<to-phone-number>"],
  message: "Hello World 👋🏻 via Sms"
}, {
  enableDeliveryReport: true //Optional parameter
});
```

Měli byste nahradit `<leased-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým k vašemu prostředku komunikačních služeb a `<to-phone-number>` telefonním číslem, na které chcete poslat zprávu. Všechny parametry telefonního čísla by měly odpovídat [standardu E. 164](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164).

`enableDeliveryReport`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .

## <a name="run-the-code"></a>Spuštění kódu

Pomocí `node` příkazu spusťte kód, který jste přidali do souboru **send-sms.js** .

```console

node ./send-sms.js

```