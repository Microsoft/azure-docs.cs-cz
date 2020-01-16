---
title: Posílání a přijímání událostí pomocí Node. js – Azure Event Hubs
description: Tento článek poskytuje návod pro vytvoření aplikace Node.js, která zasílá události ze služby Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981620"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Odesílání událostí do nebo příjem událostí z Azure Event Hubs pomocí Node. js

Azure Event Hubs je platforma pro zpracování velkých objemů dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak vytvářet aplikace v Node. js pro posílání událostí nebo přijímání událostí z centra událostí.

> [!IMPORTANT]
> V tomto rychlém startu se používá verze 5 sady SDK skriptů pro Azure Event Hubs Java. Rychlý Start, který používá starou verzi 2 sady Java Script SDK, najdete v [tomto článku](event-hubs-node-get-started-send.md). Pokud používáte verzi 2 sady SDK, doporučujeme migrovat kód na nejnovější verzi. Podrobnosti najdete v [Průvodci migrací](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md)a pak pokračujte následujícími kroky v tomto kurzu. Pak Získejte připojovací řetězec pro obor názvů centra událostí podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.


### <a name="install-npm-packages"></a>Instalace balíčků npm
Pro instalaci [balíčku npm pro Event Hubs](https://www.npmjs.com/package/@azure/event-hubs)otevřete příkazový řádek, který má v cestě `npm`, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```shell
npm install @azure/event-hubs
```

Pro přijímající stranu je potřeba nainstalovat dva další balíčky. V tomto rychlém startu použijete Azure Blob Storage pro trvalé kontrolní body, aby program nepřečetl události, které už četl. Pro přijaté zprávy v pravidelných intervalech v objektu BLOB zobrazuje kontrolní body metadata. Tento přístup usnadňuje pokračovat příjem zpráv z tam, kde jste přestali později.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Odesílání událostí

V této části se dozvíte, jak vytvořit aplikaci Node. js, která odesílá události do centra událostí.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
2. Vytvořte soubor s názvem `send.js` a vložte do něj následující kód.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Nezapomeňte nahradit **připojovací řetězec** a hodnoty **názvu centra událostí** v kódu.
5. Spusťte `node send.js` příkazu, aby se tento soubor spustil. Tím se pošle dávka tří událostí do centra událostí.
6. V Azure Portal můžete ověřit, že centrum událostí přijalo zprávy. Přepněte do zobrazení **zprávy** v části **metriky** . Aktualizujte stránku, aby se graf aktualizoval. Může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy.

    [![ověřte, že centrum událostí přijalo zprávy.](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js) .

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí
V této části se dozvíte, jak přijímat události z centra událostí pomocí úložiště kontrolních bodů Azure BLOB v aplikaci Node. js. Je kontrolní body metadat v přijatých zpráv v pravidelných intervalech v objektu Blob služby Azure Storage. Tento přístup usnadňuje pokračovat příjem zpráv z tam, kde jste přestali později.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření Azure Storage a kontejneru objektů BLOB
Pomocí těchto kroků vytvořte účet Azure Storage v něm kontejner objektů BLOB.

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Poznamenejte si připojovací řetězec a název kontejneru. Budete je používat v kódu příjmu.

### <a name="write-code-to-receive-events"></a>Psaní kódu pro příjem událostí

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
2. Vytvořte soubor s názvem `receive.js` a vložte do něj následující kód. Podrobnosti najdete v tématu komentáře ke kódu.
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
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
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
3. Nezapomeňte zadat **následující hodnoty** v kódu:
    - Připojovací řetězec k oboru názvů Event Hubs
    - Název centra událostí
    - Připojovací řetězec k účtu Azure Storage
    - Název kontejneru objektů BLOB
5. Pak spusťte příkaz `node receive.js` na příkazovém řádku, aby se tento soubor spustil. V okně byste měli vidět zprávy o přijatých událostech.

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Blahopřejeme! Nyní jste přijali události z centra událostí. Program přijímače dostane události ze všech oddílů výchozí skupiny příjemců ve vašem centru událostí.

## <a name="next-steps"></a>Další kroky
Podívejte se na tyto ukázky na GitHubu:

- [Ukázky JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Ukázky TypeScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
