---
title: Odesílání událostí do služby Azure Event Hubs pomocí Node.js | Dokumentace Microsoftu
description: Začínáme s odesíláním událostí do služby Event Hubs pomocí Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: bb5a7b477b2d19c74cc645a15cc3d891c76f28c5
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427191"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Odesílání událostí do služby Azure Event Hubs pomocí Node.js

Azure Event Hubs je vysoce škálovatelné událostí systém správy, který dokáže zpracovat miliony událostí za sekundu, které umožňuje aplikacím zpracovávat a analyzovat masivní objemy dat vytvářených připojenými zařízeními a dalšími systémy. Jakmile jsou shromážděna do centra událostí, může přijímat a zpracovávat události pomocí obslužných rutin v procesu nebo předáváním k jiným systémům pro analýzu.

Další informace o službě Event Hubs, najdete v článku [Přehled služby Event Hubs](event-hubs-about.md).

Tento kurz popisuje, jak odesílat události do centra událostí z aplikace napsané v Node.js. Příjem událostí pomocí balíčku Node.js Event Processor Host, najdete v článku [odpovídající článku Receive](event-hubs-node-get-started-receive.md).

Kód v tomto rychlém startu je k dispozici na [Githubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org).
- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][] před tím, než začnete.
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí

## <a name="create-a-namespace-and-event-hub"></a>Vytvořit obor názvů a Centrum událostí
Prvním krokem je vytvoření oboru názvů služby Event Hubs s centrem událostí pomocí webu Azure portal. Pokud některý z existujících nemáte, můžete vytvořit tyto entity podle pokynů v [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](event-hubs-create.md).

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

Zobrazit další informace o službě Event Hubs v následujících článcích:

* [Příjem událostí pomocí Node.js](event-hubs-node-get-started-receive.md)
* [Ukázky na Githubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
