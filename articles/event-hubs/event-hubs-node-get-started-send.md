---
title: Posílání a přijímání událostí pomocí Node. js – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje návod pro vytvoření aplikace Node.js, která zasílá události ze služby Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: a3233a32bf8a0e602fbdb64778fad25f550294df
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699047"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Odesílání událostí do nebo příjem událostí z Azure Event Hubs pomocí Node. js

Azure Event Hubs je platforma pro zpracování velkých objemů dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak vytvářet aplikace v Node. js pro posílání událostí nebo přijímání událostí z centra událostí.

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md)a pak pokračujte následujícími kroky v tomto kurzu. Pak Získejte připojovací řetězec pro obor názvů centra událostí podle pokynů uvedených v článku: [Získá připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.


### <a name="install-npm-package"></a>Nainstalovat balíček npm
Pro instalaci [balíčku npm pro Event Hubs](https://www.npmjs.com/package/@azure/event-hubs)otevřete příkazový řádek, který má `npm` v cestě, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```shell
npm install @azure/event-hubs
```

Pokud chcete nainstalovat [balíček npm pro hostitele procesoru událostí](https://www.npmjs.com/package/@azure/event-processor-host), spusťte následující příkaz.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Odesílání událostí

V této části se dozvíte, jak vytvořit aplikaci Node. js, která odesílá události do centra událostí. 

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `send.js` a vložte do něj následující kód. Pomocí pokynů uvedených v článku Získejte připojovací řetězec pro obor názvů centra událostí: [Získá připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu.
4. Potom spuštěním příkazu `node send.js` na příkazovém řádku spusťte tento soubor. Tato akce odešle 100 událostí do centra událostí.

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí

V této části se dozvíte, jak vytvořit aplikaci Node. js, která přijímá události z jednoho oddílu výchozí skupiny uživatelů v centru událostí. 

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `receive.js` a vložte do něj následující kód.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Do výše uvedeného kódu zadejte připojovací řetězec a název vašeho centra událostí.
4. Potom spuštěním příkazu `node receive.js` na příkazovém řádku spusťte tento soubor. Tím se v centru událostí dostanou události z jednoho z oddílů výchozí skupiny příjemců.

Blahopřejeme! Nyní jste přijali události z centra událostí.

## <a name="receive-events-using-event-processor-host"></a>Příjem událostí pomocí třídy EventProcessorHost

V této části se dozvíte, jak přijímat události z centra událostí pomocí Azure [EventProcessorHost](event-hubs-event-processor-host.md) v aplikaci Node. js. EventProcessorHost (EPH) pomáhá efektivněji přijímat události z centra událostí tak, že vytvoříte příjemce na všechny oddíly v se skupina uživatelů centra událostí. Je kontrolní body metadat v přijatých zpráv v pravidelných intervalech v objektu Blob služby Azure Storage. Tento přístup usnadňuje pokračovat příjem zpráv z tam, kde jste přestali později.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `receiveAll.js` a vložte do něj následující kód.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu spolu s připojovacím řetězcem pro Azure Blob Storage
4. Potom spuštěním příkazu `node receiveAll.js` na příkazovém řádku spusťte tento soubor.

Blahopřejeme! Nyní jste dostali události z centra událostí pomocí služby Event Processor Host. Tím se dostanou události ze všech oddílů výchozí skupiny příjemců v centru událostí.

## <a name="next-steps"></a>Další postup
Přečtěte si následující články:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
- Podívejte se na další ukázky Node. js pro [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) a [hostitele procesoru událostí](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) na GitHubu.
