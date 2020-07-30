---
title: Odesílání a příjem událostí z Azure Event Hubs pomocí JavaScriptu (starší verze)
description: Tento článek poskytuje návod pro vytvoření aplikace JavaScriptu, která odesílá a přijímá události do a z Azure Event Hubs pomocí starého balíčku Azure/Event-hub verze 2.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: ffa6b821d617731bd6ee5cbb9bec8bd6d856a7a6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414118"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Rychlý Start: odeslání událostí do nebo příjem událostí z Azure Event Hubs pomocí JavaScriptu ( @azure/event-hubs verze 2)
V tomto rychlém startu se dozvíte, jak vytvořit aplikace JavaScriptu pro odesílání událostí do a příjem událostí z centra událostí pomocí balíčku Azure/Event-Centre verze 2 JavaScriptu. 

> [!WARNING]
> V tomto rychlém startu se používá starý balíček Azure/Event-Centers verze 2. Rychlý Start, který používá nejnovější **verzi 5** balíčku, najdete v tématu [posílání a přijímání událostí pomocí Azure/eventhubs verze 5](get-started-node-send-v2.md). Chcete-li přesunout aplikaci z části použití starého balíčku na nový, přečtěte si [příručku k migraci z Azure/eventhubs verze 1 na verzi 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Předpoklady

Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- Node.js verze 8. x a vyšší. Stáhněte si nejnovější verzi LTS z nástroje [https://nodejs.org](https://nodejs.org) .
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí (IDE)
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md)a pak pokračujte následujícími kroky v tomto kurzu. Pak Získejte připojovací řetězec pro obor názvů centra událostí podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.


### <a name="install-npm-package"></a>Nainstalovat balíček npm
Pro instalaci [balíčku npm pro Event Hubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)otevřete příkazový řádek, který má `npm` v cestě, změňte adresář na složku, ve které chcete mít ukázky, a pak spusťte tento příkaz.

```shell
npm install @azure/event-hubs@2
```

Pokud chcete nainstalovat [balíček npm pro hostitele procesoru událostí](https://www.npmjs.com/package/@azure/event-processor-host), spusťte následující příkaz.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Odesílání událostí

V této části se dozvíte, jak vytvořit aplikaci JavaScriptu, která odesílá události do centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `send.js` a vložte do něj následující kód. Pomocí pokynů v článku Získejte připojovací řetězec pro obor názvů centra událostí: [získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu.
4. Potom spuštěním příkazu na `node send.js` příkazovém řádku spusťte tento soubor. Tato akce odešle 100 událostí do centra událostí.

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí

V této části se dozvíte, jak vytvořit aplikaci JavaScriptu, která přijímá události z jednoho oddílu výchozí skupiny uživatelů v centru událostí. 

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `receive.js` a vložte do něj následující kód.
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
3. Do výše uvedeného kódu zadejte připojovací řetězec a název vašeho centra událostí.
4. Potom spuštěním příkazu na `node receive.js` příkazovém řádku spusťte tento soubor. Tím se v centru událostí dostanou události z jednoho z oddílů výchozí skupiny příjemců.

Blahopřejeme! Nyní jste přijali události z centra událostí.

## <a name="receive-events-using-event-processor-host"></a>Příjem událostí pomocí třídy EventProcessorHost

V této části se dozvíte, jak přijímat události z centra událostí pomocí Azure [EventProcessorHost](event-hubs-event-processor-host.md) v aplikaci JavaScript. EventProcessorHost (EPH) pomáhá efektivně přijímat události z centra událostí tím, že vytváří přijímače napříč všemi oddíly ve skupině uživatelů centra událostí. V pravidelných intervalech v Azure Storage Blob kontrolní body v nich jsou metadata přijatých zpráv. Tento přístup usnadňuje příjem zpráv z místa, kde jste v pozdější době ponechali.

1. Otevřete oblíbený editor, například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `receiveAll.js` a vložte do něj následující kód.
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
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu spolu s připojovacím řetězcem pro Azure Blob Storage
4. Potom spuštěním příkazu na `node receiveAll.js` příkazovém řádku spusťte tento soubor.

Blahopřejeme! Nyní jste dostali události z centra událostí pomocí služby Event Processor Host. Tím se dostanou události ze všech oddílů výchozí skupiny příjemců v centru událostí.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
- Podívejte se na další ukázky JavaScriptu pro [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) a [hostitele procesoru událostí](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) na GitHubu.
