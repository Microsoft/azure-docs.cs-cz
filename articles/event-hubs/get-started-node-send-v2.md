---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí JavaScriptu (nejnovější)
description: Tento článek poskytuje návod pro vytvoření aplikace JavaScript, který odesílá a přijímá události do/z Centra událostí Azure pomocí nejnovějšího balíčku azure/event-hubs verze 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 063f2e1005db177420da7f81b799ab01fd212d7e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478478"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Odesílání událostí do event hubů nebo příjem z nich pomocí JavaScriptu (azure/event-hubs verze 5)
Tento rychlý start ukazuje, jak odesílat události do centra událostí a přijímat je pomocí balíčku JavaScript **azure/event-hubs verze 5.** 

> [!IMPORTANT]
> Tento rychlý start používá nejnovější balíček azure/event-hubs verze 5. Pro rychlý start, který používá starý balíček azure/event-hubs verze 2, najdete v tématu [Odesílání a přijímání událostí pomocí azure/event-hubs verze 2](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs tečujete, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- Node.js verze 8.x nebo novější. Stáhněte si nejnovější [verzi dlouhodobé podpory (LTS).](https://nodejs.org)  
- Visual Studio Code (doporučeno) nebo jiné integrované vývojové prostředí (IDE).  
- Aktivní obor názvů Event Hubs a centrum událostí. Chcete-li je vytvořit, proveďte následující kroky: 

   1. Na [webu Azure Portal](https://portal.azure.com)vytvořte obor názvů typu *Event Hubs*a potom získejte přihlašovací údaje pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. 
   1. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů na [úvodním panelu: Vytvořte centrum událostí pomocí portálu Azure .](event-hubs-create.md)
   1. Pokračujte podle pokynů v tomto rychlém startu. 
   1. Chcete-li získat připojovací řetězec pro obor názvů centra událostí, postupujte podle pokynů v [části Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Zaznamenejte připojovací řetězec, který chcete použít později v tomto rychlém startu.
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získejte **připojovací řetězec pro obor názvů Event Hubs** podle následujících pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

### <a name="install-the-npm-package"></a>Instalace balíčku npm
Chcete-li nainstalovat [balíček Správce balíčků uzlů (npm) pro centra událostí](https://www.npmjs.com/package/@azure/event-hubs), otevřete příkazový řádek, který má v cestě *npm,* změňte adresář na složku, do které chcete uchovávat vzorky, a spusťte tento příkaz:

```shell
npm install @azure/event-hubs
```

Pro přijímající stranu je třeba nainstalovat další dva balíčky. V tomto rychlém startu pomocí úložiště objektů blob Azure zachovat kontrolní body tak, aby program nečte události, které již četl. Provádí kontrolní body metadat na přijaté zprávy v pravidelných intervalech v objektu blob. Tento přístup usnadňuje další příjem zpráv později z místa, kde jste skončili.

Spusťte následující příkazy:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Odesílání událostí

V této části vytvoříte aplikaci JavaScript, která odesílá události do centra událostí.

1. Otevřete svůj oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
1. Vytvořte soubor s názvem *send.js*a vložte do něj následující kód:

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
1. V kódu použijte skutečné hodnoty nahradit následující:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Spuštění `node send.js` tohoto souboru spustit. Tento příkaz odešle dávku tří událostí do centra událostí.
1. Na webu Azure Portal ověřte, že centrum událostí přijalo zprávy. V části **Metriky** přepněte do zobrazení **Zprávy.** Aktualizujte stránku a aktualizujte graf. Může trvat několik sekund, než se zobrazí, že zprávy byly přijaty.

    [![Ověřte, zda centrum událostí přijalo zprávy](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód, včetně dalších informačních komentářů, najdete na [stránce GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí
V této části přijímáte události z centra událostí pomocí úložiště úložiště objektů Blob Azure v aplikaci JavaScript. Provádí kontrolní body metadat na přijaté zprávy v pravidelných intervalech v objektu blob úložiště Azure. Tento přístup usnadňuje další příjem zpráv později z místa, kde jste skončili.

> [!NOTE]
> Pokud používáte azure zásobníku, tato platforma může podporovat jinou verzi sady Storage Blob SDK než ty, které jsou obvykle k dispozici v Azure. Například pokud používáte [ve verzi Azure Stack Hub 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze pro službu Storage je verze 2017-11-09. V takovém případě kromě následujících kroků v této části budete muset také přidat kód pro cílrozhraní API služby úložiště verze 2017-11-09. Příklad, jak cílit na konkrétní verzi rozhraní API úložiště, najdete [v tématu JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) a ukázky [jazyka Na](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) GitHubu. Další informace o verzích služeb Azure Storage, které jsou podporované v centru Azure Stack Hub, najdete v centru [Azure Stack Hub: Rozdíly a důležité informace](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Vytvoření účtu úložiště Azure a kontejneru objektů blob
Pokud chcete vytvořit účet úložiště Azure a kontejner objektů blob v něm, proveďte následující akce:

1. [Vytvoření účtu úložiště Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Vytvoření kontejneru objektů blob v účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Nezapomeňte zaznamenat připojovací řetězec a název kontejneru pro pozdější použití v přijímaném kódu.

### <a name="write-code-to-receive-events"></a>Napsat kód pro příjem událostí

1. Otevřete svůj oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com).
1. Vytvořte soubor s názvem *receive.js*a vložte do něj následující kód:

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
1. V kódu použijte skutečné hodnoty k nahrazení následujících hodnot:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Spuštění `node receive.js` tohoto souboru v příkazovém řádku Okno by mělo zobrazovat zprávy o přijatých událostech.

    > [!NOTE]
    > Úplný zdrojový kód, včetně dalších informačních komentářů, najdete na [stránce GitHub receiveEventsUsingCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Blahopřejeme! Nyní jste obdrželi události z centra událostí. Program příjemce bude přijímat události ze všech oddílů výchozí skupiny příjemců v centru událostí.

## <a name="next-steps"></a>Další kroky
Podívejte se na tyto ukázky na GitHubu:

- [Ukázky JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Ukázky typu Script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
