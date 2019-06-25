---
title: Odesílání a příjem událostí pomocí Node.js – Azure Event Hubs | Dokumentace Microsoftu
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
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539334"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Odesílání událostí do nebo přijímat události z Azure Event Hubs pomocí Node.js

Azure Event Hubs je velké objemy dat, streamování platformy a služba pro ingestování událostí, které můžou přijímat a zpracovávat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak vytvářet aplikace Node.js na odesílání událostí do nebo přijímat události z centra událostí.

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (doporučeno) nebo jakékoli jiné integrované vývojové prostředí
- **Vytvořit obor názvů služby Event Hubs a centra událostí**. Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md), pak pokračujte následujícími kroky v tomto kurzu. Potom získejte připojovací řetězec pro obor názvů centra událostí podle pokynů v článku: [Získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.


### <a name="install-npm-package"></a>Instalace balíčku npm
Chcete-li nainstalovat [balíčku npm pro službu Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), otevřete příkazový řádek, který má `npm` v cestě, změňte adresář na složku, ve kterém chcete mít vašich ukázek a spusťte tento příkaz

```shell
npm install @azure/event-hubs
```

K instalaci [balíčku npm pro Event Processor Host](https://www.npmjs.com/package/@azure/event-processor-host), spusťte následující příkaz místo

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Odesílání událostí

Tato část ukazuje, jak vytvořit aplikaci v Node.js, která zasílá události do centra událostí. 

1. Otevřete oblíbeného editoru, jako například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `send.js` a vložte níže uvedeného kódu do něj.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu
4. Pak spusťte příkaz `node send.js` v příkazovém řádku a spusťte tento soubor. Tento tok odešle 100 události do vašeho centra událostí

Blahopřejeme! Nyní jste odeslali události do centra událostí.


## <a name="receive-events"></a>Příjem událostí

Tato část ukazuje, jak vytvořit aplikaci v Node.js, která bude přijímat události z jednoho oddílu výchozí skupinu spotřebitelů v Centru událostí. 

1. Otevřete oblíbeného editoru, jako například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `receive.js` a vložte níže uvedeného kódu do něj.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu.
4. Pak spusťte příkaz `node receive.js` v příkazovém řádku a spusťte tento soubor. Tím se zobrazí události od jednoho z oddílů výchozí skupinu uživatelů do vašeho centra událostí

Blahopřejeme! Obdrželi jste nyní událostí z centra událostí.

## <a name="receive-events-using-event-processor-host"></a>Příjem událostí pomocí třídy Event Processor Host

Tato část ukazuje, jak přijímat události z centra událostí pomocí služby Azure [EventProcessorHost](event-hubs-event-processor-host.md) v aplikaci Node.js. EventProcessorHost (EPH) pomáhá efektivněji přijímat události z centra událostí tak, že vytvoříte příjemce na všechny oddíly v se skupina uživatelů centra událostí. Je kontrolní body metadat v přijatých zpráv v pravidelných intervalech v objektu Blob služby Azure Storage. Tento přístup usnadňuje pokračovat příjem zpráv z tam, kde jste přestali později.

1. Otevřete oblíbeného editoru, jako například [Visual Studio Code](https://code.visualstudio.com). 
2. Vytvořte soubor s názvem `receiveAll.js` a vložte níže uvedeného kódu do něj.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Zadejte připojovací řetězec a název vašeho centra událostí ve výše uvedeném kódu společně s připojovací řetězec pro služby Azure Blob Storage
4. Pak spusťte příkaz `node receiveAll.js` v příkazovém řádku a spusťte tento soubor.

Blahopřejeme! Obdrželi jste nyní událostí z centra událostí pomocí třídy Event Processor Host. Tím se zobrazí události ze všech oddílů výchozí skupinu uživatelů do vašeho centra událostí

## <a name="next-steps"></a>Další postup
V následujících článcích:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologii používané v Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
- Podívejte se na další ukázky v Node.js pro [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) a [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) na Githubu
