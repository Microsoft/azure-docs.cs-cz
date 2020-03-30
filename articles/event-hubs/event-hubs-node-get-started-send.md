---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí JavaScriptu (starší verze)
description: Tento článek poskytuje návod pro vytvoření aplikace JavaScript, který odesílá nebo přijímá události do/z Centra událostí Azure pomocí starého balíčku azure/event-hubs verze 2.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162612"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Úvodní příručka: Odesílání událostí do nebo přijímání událostí@azure/event-hubs z Azure Event Hubs pomocí JavaScriptu (verze 2)
Tento rychlý start ukazuje, jak vytvořit javascriptové aplikace pro odesílání událostí a příjem událostí z centra událostí pomocí balíčku JavaScript azure/event-hubs verze 2. 

> [!WARNING]
> Tento rychlý start používá starý balíček azure/event-hubs verze 2. Pro rychlý start, který používá nejnovější **verzi 5** balíčku, najdete v tématu odesílání a přijímání událostí [pomocí azure/eventhubs verze 5](get-started-node-send-v2.md). Chcete-li přesunout aplikaci z použití starého balíčku na nový, naleznete [v průvodci pro migraci z azure/eventhubs verze 1 na verzi 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Požadavky

Pokud s Azure Event Hubs tečujete, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- Node.js verze 8.x a vyšší. Stáhněte si nejnovější [https://nodejs.org](https://nodejs.org)verzi LTS z aplikace .
- Visual Studio Kód (doporučeno) nebo jakékoliv jiné IDE
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md)a pokračujte následujícími kroky v tomto kurzu. Potom získat připojovací řetězec pro obor názvů centra událostí podle následujících pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete dále v tomto kurzu.


### <a name="install-npm-package"></a>Instalace balíčku npm
Chcete-li nainstalovat [balíček npm pro centra](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)událostí `npm` , otevřete příkazový řádek, který má v cestě, změňte adresář na složku, ve které chcete mít vzorky, a spusťte tento příkaz.

```shell
npm install @azure/event-hubs@2
```

Chcete-li nainstalovat [balíček npm pro hostitele procesoru událostí](https://www.npmjs.com/package/@azure/event-processor-host), spusťte příkaz níže.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Odesílání událostí

V této části se zobrazí způsob vytvoření javascriptové aplikace, která odesílá události do centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

1. Otevřete svůj oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor `send.js` s názvem a vložte do něj níže uvedený kód. Získejte připojovací řetězec pro obor názvů centra událostí podle následujících pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

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
3. Do výše uvedeného kódu zadejte připojovací řetězec a název centra událostí.
4. Potom spusťte příkaz `node send.js` v příkazovém řádku a spusťte tento soubor. Tím se do centra událostí odešle 100 událostí.

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí

Tato část ukazuje, jak vytvořit aplikaci Jazyka JavaScript, která přijímá události z jednoho oddílu výchozí skupiny spotřebitelů v centru událostí. 

1. Otevřete svůj oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor `receive.js` s názvem a vložte do něj níže uvedený kód.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

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
3. Do výše uvedeného kódu zadejte připojovací řetězec a název centra událostí.
4. Potom spusťte příkaz `node receive.js` v příkazovém řádku a spusťte tento soubor. To bude přijímat události z jednoho z oddílů výchozí skupiny spotřebitelů v centru událostí

Blahopřejeme! Nyní jste obdrželi události z centra událostí.

## <a name="receive-events-using-event-processor-host"></a>Příjem událostí pomocí třídy EventProcessorHost

Tato část ukazuje, jak přijímat události z centra událostí pomocí Azure [EventProcessorHost](event-hubs-event-processor-host.md) v aplikaci JavaScript. EventProcessorHost (EPH) pomáhá efektivně přijímat události z centra událostí vytvořením přijímačů ve všech oddílech ve skupině příjemců centra událostí. Kontrolní body metadata na přijaté zprávy v pravidelných intervalech v objektu blob úložiště Azure. Tento přístup usnadňuje další příjem zpráv z místa, kde jste skončili později.

1. Otevřete svůj oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor `receiveAll.js` s názvem a vložte do něj níže uvedený kód.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

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
3. Zadejte připojovací řetězec a název centra událostí ve výše uvedeném kódu spolu s připojovacím řetězcem pro azure blob storage
4. Potom spusťte příkaz `node receiveAll.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Nyní jste obdrželi události z centra událostí pomocí hostitele procesoru událostí. To bude přijímat události ze všech oddílů výchozí skupiny spotřebitelů v centru událostí

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
- Podívejte se na další ukázky JavaScriptu pro [centra událostí](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) a [hostitele procesoru událostí](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) na GitHubu
