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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677897"
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
- Klonování [úložišti ukázek Githubu](https://github.com/Azure/azure-event-hubs-node) na svém počítači. 


## <a name="send-events"></a>Odesílání událostí
Tato část ukazuje, jak vytvořit aplikaci v Node.js, která zasílá události do centra událostí. 

### <a name="install-nodejs-package"></a>Nainstalujte balíček Node.js
Nainstalujte balíček Node.js pro Azure Event Hubs na svém počítači. 

```shell
npm install @azure/event-hubs
```

Pokud ještě jste naklonovali úložiště Git, jak je uvedeno v tomto požadavku, stáhněte si [ukázka](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) z Githubu. 

Sady SDK, které jste naklonovali obsahuje několik ukázek, které ukazují, jak odesílat události do centra událostí pomocí node.js. V tomto rychlém startu použijete **simpleSender.js** příklad. Sledovat události přijímané, otevřete další terminálu a příjem událostí pomocí [přijímat ukázka](event-hubs-node-get-started-receive.md).

1. Otevřete projekt ve Visual Studio Code. 
2. Vytvořte soubor s názvem **.env** pod **klienta** složky. Zkopírujte a vložte vzorek proměnné prostředí z **sample.env** v kořenové složce.
3. Nakonfigurujte připojovací řetězec centra událostí, název centra událostí a koncový bod úložiště. Postup získání připojovacího řetězce pro Centrum událostí [získání připojovacího řetězce](event-hubs-create.md#create-an-event-hubs-namespace).
4. Přejděte na svém rozhraní příkazového řádku Azure **klienta** cesta ke složce. Instalace balíčků uzlu a sestavte projekt spuštěním následujících příkazů:

    ```shell
    npm i
    npm run build
    ```
5. Zahájit odesílání událostí spuštěním následujícího příkazu: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Revize ukázkového kódu 
Projděte si ukázkový kód v souboru simpleSender.js k odesílání událostí do centra událostí.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Nezapomeňte nastavit proměnné prostředí před spuštěním skriptu. Můžete je nakonfigurovat pomocí příkazového řádku, jak je znázorněno v následujícím příkladu, nebo použít [dotenv balíčku](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Příjem událostí
Tento kurz ukazuje, jak přijímat události z centra událostí pomocí služby Azure [EventProcessorHost](event-hubs-event-processor-host.md) v aplikaci Node.js. EventProcessorHost (EPH) pomáhá efektivněji přijímat události z centra událostí tak, že vytvoříte příjemce na všechny oddíly v se skupina uživatelů centra událostí. Je kontrolní body metadat v přijatých zpráv v pravidelných intervalech v objektu Blob služby Azure Storage. Tento přístup usnadňuje pokračovat příjem zpráv z tam, kde jste přestali později.

Kód v tomto rychlém startu je k dispozici na [Githubu](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Naklonujte úložiště Git
Stáhněte nebo naklonujte [ukázka](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) z Githubu. 

### <a name="install-the-eventprocessorhost"></a>Nainstalujte třídy EventProcessorHost
Nainstalujte třídy EventProcessorHost služby Event Hubs modulu. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Příjem událostí pomocí třídy EventProcessorHost
Sady SDK, které jste naklonovali obsahuje několik ukázek, které ukazují, jak přijímat události z centra událostí pomocí Node.js. V tomto rychlém startu použijete **singleEPH.js** příklad. Sledovat události přijímané, otevřete další terminálu a odesílat události pomocí [poslat vzorek](event-hubs-node-get-started-send.md).

1. Otevřete projekt ve Visual Studio Code. 
2. Vytvořte soubor s názvem **.env** pod **procesoru** složky. Zkopírujte a vložte vzorek proměnné prostředí z **sample.env** v kořenové složce.
3. Nakonfigurujte připojovací řetězec centra událostí, název centra událostí a koncový bod úložiště. Připojovací řetězec můžete zkopírovat z centra událostí **připojovací řetězec – primární** klíč **RootManageSharedAccessKey** na stránce centra událostí na webu Azure Portal. Podrobné pokyny najdete v článku [získání připojovacího řetězce](event-hubs-create.md#create-an-event-hubs-namespace).
4. Přejděte na svém rozhraní příkazového řádku Azure **procesoru** cesta ke složce. Instalace balíčků uzlu a sestavte projekt spuštěním následujících příkazů:

    ```shell
    npm i
    npm run build
    ```
5. Příjem událostí pomocí vaší třídy event processor host spuštěním následujícího příkazu:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Revize ukázkového kódu 
Tady je ukázkový kód pro příjem událostí z centra událostí pomocí node.js. Můžete ručně vytvořit soubor sampleEph.js a spusťte ho, abyste příjem událostí do centra událostí. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Nezapomeňte nastavit proměnné prostředí před spuštěním skriptu. Můžete nastavit tuto konfiguraci na příkazovém řádku, jak je znázorněno v následujícím příkladu, nebo použít [dotenv balíčku](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Můžete najít další ukázky [tady](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Další postup
V následujících článcích:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologii používané v Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
- Prohlédněte si další ukázky Node.js pro službu Event Hubs na [Githubu](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
