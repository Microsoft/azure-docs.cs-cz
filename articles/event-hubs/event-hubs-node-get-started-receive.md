---
title: Příjem událostí pomocí Node.js – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje návod pro vytvoření aplikace Node.js, která bude přijímat události z Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 55e4ce4a59a498c5c22fe6e701e3140aa65e7a10
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895240"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Příjem událostí z Azure Event Hubs pomocí Node.js

Azure Event Hubs je vysoce škálovatelné událostí systém správy, který dokáže zpracovat miliony událostí za sekundu, které umožňuje aplikacím zpracovávat a analyzovat masivní objemy dat vytvářených připojenými zařízeními a dalšími systémy. Jakmile jsou shromážděna do centra událostí, může přijímat a zpracovávat události pomocí obslužných rutin v procesu nebo předáváním k jiným systémům pro analýzu. Můžete také zachytit data událostí ve službě Azure Storage nebo Azure Data Lake Store předtím, než se zpracovává.  

Další informace o službě Event Hubs, najdete v článku [Přehled služby Event Hubs](event-hubs-about.md).

Tento kurz ukazuje, jak přijímat události z centra událostí pomocí služby Azure [EventProcessorHost](event-hubs-event-processor-host.md) v aplikaci Node.js. EventProcessorHost (EPH) pomáhá efektivněji přijímat události z centra událostí tak, že vytvoříte příjemce na všechny oddíly v se skupina uživatelů centra událostí. Je kontrolní body metadat v přijatých zpráv v pravidelných intervalech v objektu Blob služby Azure Storage. Tento přístup usnadňuje pokračovat příjem zpráv z tam, kde jste přestali později.

Kód v tomto rychlém startu je k dispozici na [Githubu](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  K odesílání událostí do služby Event Hubs pomocí Node.js, najdete v tomto článku: [Odesílání událostí do služby Azure Event Hubs pomocí Node.js](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Verze Node.js 8.x a vyšší. Stáhněte si nejnovější verzi LTS pomocí [ https://nodejs.org ](https://nodejs.org). Nepoužívejte starší verzi node.js LTS. 
- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][] před tím, než začnete.

## <a name="create-a-namespace-and-event-hub"></a>Vytvořit obor názvů a Centrum událostí
Prvním krokem je vytvoření oboru názvů služby Event Hubs s centrem událostí pomocí webu Azure portal. Pokud některý z existujících nemáte, můžete vytvořit tyto entity podle pokynů v [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru
Použití třídy EventProcessorHost, musí mít účet služby Azure Storage. Informace o stavu, jako je zapůjčení na oddíly a kontrolní body v datovém proudu událostí jsou sdílené mezi příjemci pomocí kontejneru služby Azure Storage. Účet úložiště Azure můžete vytvořit podle pokynů v [v tomto článku](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Naklonujte úložiště Git
Stáhněte nebo naklonujte [ukázka](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) z Githubu. 

## <a name="install-the-eventprocessorhost"></a>Nainstalujte třídy EventProcessorHost
Nainstalujte třídy EventProcessorHost služby Event Hubs modulu. 

```shell
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Příjem událostí pomocí třídy EventProcessorHost
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

## <a name="review-the-sample-code"></a>Revize ukázkového kódu 
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

V tomto rychlém startu jste vytvořili aplikaci Node.js, která se zobrazila zprávy z centra událostí. Zjistěte, jak odesílat události do centra událostí pomocí Node.js, naleznete v tématu [odesílání událostí z centra událostí - Node.js](event-hubs-node-get-started-send.md).

<!-- Links -->
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
