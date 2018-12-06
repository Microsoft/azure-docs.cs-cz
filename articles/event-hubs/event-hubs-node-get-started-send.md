---
title: Odesílání událostí do služby Azure Event Hubs pomocí Node.js | Dokumentace Microsoftu
description: Začínáme s odesíláním událostí do služby Event Hubs pomocí Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 12/05/2018
ms.author: shvija
ms.openlocfilehash: e64aab3aed582a60140ee1357e79ee5ee4a4cdf4
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965032"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Odesílání událostí do služby Azure Event Hubs pomocí Node.js

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak odesílat události do centra událostí z aplikace napsané v Node.js.

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md) a pak pokračujte podle následujících pokynů v tomto kurzu.

Získání připojovacího řetězce pro obor názvů centra událostí podle pokynů v článku: [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.

## <a name="clone-the-sample-git-repository"></a>Naklonujte ukázkové úložiště Git
Naklonujte ukázkové úložiště Git ze [Githubu](https://github.com/Azure/azure-event-hubs-node) na svém počítači. 

## <a name="install-nodejs-package"></a>Nainstalujte balíček Node.js
Nainstalujte balíček Node.js pro Azure Event Hubs na svém počítači. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Naklonujte úložiště Git
Stáhněte nebo naklonujte [ukázka](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) z Githubu. 

## <a name="send-events"></a>Odesílání událostí
Sady SDK, které jste naklonovali obsahuje několik ukázek, které ukazují, jak odesílat události do centra událostí pomocí node.js. V tomto rychlém startu použijete **simpleSender.js** příklad. Sledovat události přijímané, otevřete další terminálu a příjem událostí pomocí [přijímat ukázka](event-hubs-node-get-started-receive.md).

1. Otevřete projekt ve Visual Studio Code. 
2. Vytvořte soubor s názvem **.env** pod **klienta** složky. Zkopírujte a vložte vzorek proměnné prostředí z **sample.env** v kořenové složce.
3. Nakonfigurujte připojovací řetězec centra událostí, název centra událostí a koncový bod úložiště. Připojovací řetězec můžete zkopírovat z centra událostí **připojovací řetězec – primární** klíč **RootManageSharedAccessKey** na stránce centra událostí na webu Azure Portal. Podrobné pokyny najdete v článku [získání připojovacího řetězce](event-hubs-create.md#create-an-event-hubs-namespace).
4. Přejděte na svém rozhraní příkazového řádku Azure **klienta** cesta ke složce. Instalace balíčků uzlu a sestavte projekt spuštěním následujících příkazů:

    ```nodejs
    npm i
    npm run build
    ```
5. Zahájit odesílání událostí spuštěním následujícího příkazu: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Revize ukázkového kódu 
Tady je ukázkový kód pro odesílání událostí do centra událostí pomocí node.js. Můžete ručně vytvořit soubor sampleSender.js a spusťte ho k odesílání událostí do centra událostí. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

Nezapomeňte nastavit proměnné prostředí před spuštěním skriptu. Můžete nastavit tuto konfiguraci na příkazovém řádku, jak je znázorněno v následujícím příkladu, nebo použít [dotenv balíčku](https://www.npmjs.com/package/dotenv#dotenv). 

```
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
