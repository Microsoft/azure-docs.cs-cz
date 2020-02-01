---
title: Posílání nebo přijímání událostí z Azure Event Hubs s využitím Node. js (nejnovější)
description: Tento článek popisuje návod pro vytvoření aplikace Node. js, která odesílá a přijímá události do a z Azure Event Hubs pomocí nejnovějšího balíčku Azure/Event-hub verze 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906366"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Posílání událostí a přijímání událostí z Center událostí pomocí Node. js (Azure/Event – centra verze 5)

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Centra událostí můžou zpracovávat a ukládat události, data nebo telemetrie vytvářené distribuovaným softwarem a zařízeními. Data odesílaná do centra událostí je možné transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase nebo adaptérů pro dávkování či ukládání. Další informace najdete v tématu [Event Hubs přehled](event-hubs-about.md) a [Event Hubs funkce](event-hubs-features.md).

V tomto rychlém startu se dozvíte, jak vytvářet aplikace v Node. js, které mohou odesílat události do nebo přijímat události z centra událostí.

> [!IMPORTANT]
> V tomto rychlém startu se používá verze 5 sady Azure Event Hubs JavaScript SDK. Rychlý Start, který používá verzi 2 sady JavaScript SDK, najdete v [tomto článku](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- Předplatné Azure. Pokud ho nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.  
- Node. js verze 8. x nebo novější. Stáhněte si nejnovější [verzi LTS (Long-Term support)](https://nodejs.org).  
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí (IDE).  
- Aktivní Event Hubs obor názvů a centrum událostí. Pokud je chcete vytvořit, proveďte následující kroky: 

   1. V [Azure Portal](https://portal.azure.com)vytvořte obor názvů typu *Event Hubs*a pak Získejte přihlašovací údaje pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. 
   1. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md).
   1. Pokračujte podle pokynů v tomto rychlém startu. 
   1. Pokud chcete získat připojovací řetězec pro obor názvů centra událostí, postupujte podle pokynů v části [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Poznamenejte si připojovací řetězec pro pozdější použití v tomto rychlém startu.

### <a name="install-the-npm-package"></a>Instalace balíčku npm
Chcete-li nainstalovat [balíček správce balíčků Node (npm) pro Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), otevřete příkazový řádek, který má v cestě *npm* , změňte adresář na složku, do které chcete uchovávat ukázky, a pak spusťte tento příkaz:

```shell
npm install @azure/event-hubs
```

Pro přijímající stranu je potřeba nainstalovat dva další balíčky. V tomto rychlém startu použijete úložiště objektů BLOB v Azure k trvalému kontrolnímu bodu, aby program nečetl události, které už četl. Pro přijaté zprávy v pravidelných intervalech v objektu BLOB provádí kontrolní body metadat. Tento přístup usnadňuje příjem zpráv později od místa, kde jste skončili.

Spusťte následující příkazy:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Odesílání událostí

V této části vytvoříte aplikaci Node. js, která odesílá události do centra událostí.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
1. Vytvořte soubor s názvem *Send. js*a vložte do něj následující kód:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. V kódu použijte reálné hodnoty a nahraďte následující:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Spusťte `node send.js`, aby se tento soubor spustil. Tento příkaz pošle do centra událostí dávku tří událostí.
1. V Azure Portal ověřte, zda centrum událostí přijalo zprávy. V části **metriky** přepněte na zobrazení **zprávy** . Aktualizujte stránku, aby se graf aktualizoval. Může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy.

    [![ověřte, že centrum událostí přijalo zprávy.](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód, včetně dalších informativních komentářů, najdete na [stránce GitHub sendEvents. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí
V této části obdržíte události z centra událostí pomocí úložiště kontrolních bodů služby Azure Blob Storage v aplikaci Node. js. Pro přijaté zprávy v pravidelných intervalech v objektu blob Azure Storage provádí kontrolní body metadat. Tento přístup usnadňuje příjem zpráv později od místa, kde jste skončili.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Vytvoření účtu služby Azure Storage a kontejneru objektů BLOB
Pokud chcete vytvořit účet úložiště Azure a kontejner objektů BLOB v něm, proveďte následující akce:

1. [Vytvoření účtu služby Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Vytvoření kontejneru objektů BLOB v účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Nezapomeňte si poznamenejte připojovací řetězec a název kontejneru pro pozdější použití v kódu pro příjem.

### <a name="write-code-to-receive-events"></a>Psaní kódu pro příjem událostí

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
1. Vytvořte soubor s názvem *Receive. js*a vložte do něj následující kód:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. V kódu pomocí reálných hodnot nahraďte následující hodnoty:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Spusťte `node receive.js` na příkazovém řádku, aby se tento soubor spustil. V okně by se měly zobrazovat zprávy o přijatých událostech.

    > [!NOTE]
    > Úplný zdrojový kód, včetně dalších informativních komentářů, najdete na [stránce GitHub receiveEventsUsingCheckpointStore. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Blahopřejeme! Nyní jste dostali události z centra událostí. Program přijímače dostane události ze všech oddílů výchozí skupiny příjemců v centru událostí.

## <a name="next-steps"></a>Další kroky
Podívejte se na tyto ukázky na GitHubu:

- [Ukázky JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Ukázky TypeScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
