---
title: Posílání a přijímání událostí z Azure Event Hubs pomocí JavaScriptu (nejnovější)
description: Tento článek popisuje návod pro vytvoření aplikace JavaScriptu, která odesílá a přijímá události do a z Azure Event Hubs pomocí nejnovějšího balíčku Azure/Event-Centers.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2e5f297d5edb9a271843db060f948209dd076074
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106083"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>Odesílání událostí do a příjem událostí z Center událostí pomocí JavaScriptu (Azure/Event-hub)
V tomto rychlém startu se dozvíte, jak odesílat události do a přijímat události z centra událostí pomocí balíčku JavaScriptu pro **Azure/Event-Center** . 


## <a name="prerequisites"></a>Předpoklady
Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- Node.js verze 8. x nebo novější. Stáhněte si nejnovější [verzi LTS (Long-Term support)](https://nodejs.org).  
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí (IDE).  
- Aktivní Event Hubs obor názvů a centrum událostí. Pokud je chcete vytvořit, proveďte následující kroky: 

   1. V [Azure Portal](https://portal.azure.com)vytvořte obor názvů typu *Event Hubs* a pak Získejte přihlašovací údaje pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. 
   1. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md).
   1. Pokračujte podle pokynů v tomto rychlém startu. 
   1. Pokud chcete získat připojovací řetězec pro obor názvů centra událostí, postupujte podle pokynů v části [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Poznamenejte si připojovací řetězec pro pozdější použití v tomto rychlém startu.
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte **připojovací řetězec pro obor názvů Event Hubs** podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

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

V této části vytvoříte aplikaci JavaScriptu, která odesílá události do centra událostí.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
1. Vytvořte soubor s názvem *send.js* a vložte do něj následující kód:

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
1. Spusťte `node send.js` , aby se tento soubor spustil. Tento příkaz pošle do centra událostí dávku tří událostí.
1. V Azure Portal ověřte, zda centrum událostí přijalo zprávy. V části **metriky** přepněte na zobrazení **zprávy** . Aktualizujte stránku, aby se graf aktualizoval. Může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy.

    [![Ověřte, že centrum událostí přijalo zprávy.](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód včetně dalších informativních komentářů najdete na [stránce sendEvents.js GitHubu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí
V této části obdržíte události z centra událostí pomocí úložiště kontrolních bodů služby Azure Blob Storage v aplikaci JavaScript. Pro přijaté zprávy v pravidelných intervalech v objektu blob Azure Storage provádí kontrolní body metadat. Tento přístup usnadňuje příjem zpráv později od místa, kde jste skončili.

> [!WARNING]
> Pokud spustíte tento kód v Azure Stackovém centru, dojde k chybám za běhu, pokud necílíte na konkrétní verzi rozhraní API úložiště. Důvodem je, že sada Event Hubs SDK používá nejnovější dostupné rozhraní API Azure Storage dostupné v Azure, které nemusí být k dispozici na vaší platformě služby Azure Stack hub. Centrum Azure Stack může podporovat jinou verzi sady SDK pro úložiště objektů blob, než jsou ta, která jsou běžně dostupná v Azure. Pokud jako úložiště kontrolního bodu používáte Azure blogu Storage, podívejte se na [podporovanou verzi rozhraní API Azure Storage pro sestavení centra Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) a cílení na verzi v kódu. 
>
> Pokud například používáte v Azure Stack centra verze 2005, nejvyšší dostupná verze služby úložiště je verze 2019-02-02. Ve výchozím nastavení používá Klientská knihovna Event Hubs SDK nejvyšší dostupnou verzi v Azure (2019-07-07 v době vydání sady SDK). V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2019-02-02. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v tématu ukázky pro [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) a  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) na GitHubu. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Vytvoření účtu služby Azure Storage a kontejneru objektů BLOB
Pokud chcete vytvořit účet úložiště Azure a kontejner objektů BLOB v něm, proveďte následující akce:

1. [Vytvoření účtu úložiště Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Vytvoření kontejneru objektů blob v účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md)

Nezapomeňte si poznamenejte připojovací řetězec a název kontejneru pro pozdější použití v kódu pro příjem.

### <a name="write-code-to-receive-events"></a>Psaní kódu pro příjem událostí

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
1. Vytvořte soubor s názvem *receive.js* a vložte do něj následující kód:

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
1. Spusťte `node receive.js` příkaz na příkazovém řádku, aby se tento soubor spustil. V okně by se měly zobrazovat zprávy o přijatých událostech.

    > [!NOTE]
    > Úplný zdrojový kód včetně dalších informativních komentářů najdete na [stránce receiveEventsUsingCheckpointStore.js GitHubu](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Blahopřejeme! Nyní jste dostali události z centra událostí. Program přijímače dostane události ze všech oddílů výchozí skupiny příjemců v centru událostí.

## <a name="next-steps"></a>Další kroky
Podívejte se na tyto ukázky na GitHubu:

- [Ukázky JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Ukázky TypeScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
