---
title: Odesílání událostí pomocí Node.js – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje návod pro vytvoření aplikace Node.js, která zasílá události ze služby Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447714"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Odesílání událostí do služby Azure Event Hubs pomocí Node.js

Azure Event Hubs je velké objemy dat, streamování platformy a služba pro ingestování událostí, které můžou přijímat a zpracovávat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak odesílat události do centra událostí z aplikace napsané v Node.js.

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md), pak pokračujte následujícími kroky v tomto kurzu.

Získání připojovacího řetězce pro obor názvů centra událostí podle pokynů v článku: [Získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.

## <a name="clone-the-sample-git-repository"></a>Naklonujte ukázkové úložiště Git
Naklonujte ukázkové úložiště Git ze [Githubu](https://github.com/Azure/azure-event-hubs-node) na svém počítači. 

## <a name="install-nodejs-package"></a>Nainstalujte balíček Node.js
Nainstalujte balíček Node.js pro Azure Event Hubs na svém počítači. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Naklonujte úložiště Git
Stáhněte nebo naklonujte [ukázka](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) z Githubu. 

## <a name="send-events"></a>Odesílání událostí
Sady SDK, které jste naklonovali obsahuje několik ukázek, které ukazují, jak odesílat události do centra událostí pomocí node.js. V tomto rychlém startu použijete **simpleSender.js** příklad. Sledovat události přijímané, otevřete další terminálu a příjem událostí pomocí [přijímat ukázka](event-hubs-node-get-started-receive.md).

1. Otevřete projekt ve Visual Studio Code. 
2. Vytvořte soubor s názvem **.env** pod **klienta** složky. Zkopírujte a vložte vzorek proměnné prostředí z **sample.env** v kořenové složce.
3. Nakonfigurujte připojovací řetězec centra událostí, název centra událostí a koncový bod úložiště. Postup získání připojovacího řetězce pro Centrum událostí [získání připojovacího řetězce](event-hubs-create.md#create-an-event-hubs-namespace).
4. Přejděte na svém rozhraní příkazového řádku Azure **klienta** cesta ke složce. Instalace balíčků uzlu a sestavte projekt spuštěním následujících příkazů:

    ```shell
    npm i
    npm run build
    ```
5. Zahájit odesílání událostí spuštěním následujícího příkazu: 

    ```shell
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Revize ukázkového kódu 
Projděte si ukázkový kód v souboru simpleSender.js k odesílání událostí do centra událostí.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Nezapomeňte nastavit proměnné prostředí před spuštěním skriptu. Můžete je nakonfigurovat pomocí příkazového řádku, jak je znázorněno v následujícím příkladu, nebo použít [dotenv balíčku](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste odeslali zprávy do centra událostí pomocí Node.js. Další způsob příjmu událostí z centra událostí pomocí Node.js najdete v tématu [přijímat události z centra událostí – Node.js](event-hubs-node-get-started-receive.md)

Prohlédněte si další ukázky Node.js pro službu Event Hubs na [Githubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
