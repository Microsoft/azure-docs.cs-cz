---
title: Rychlý Start knihovny lineární pro JavaScript detektoru anomálií
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 03fbd5e641c72a03a4a3cb19219678bc3d3fff51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732346"
---
Začněte s klientskou knihovnou anomálií lineární pro JavaScript. Pomocí těchto kroků balíček nainstalujte a začněte používat algoritmy poskytované službou. Nová rozhraní API pro detekci anomálií lineární umožňují vývojářům snadnou integraci pokročilého AI pro detekci anomálií ze skupin metrik, aniž by to mělo znalosti strojového učení nebo data s popisky. Závislosti a vzájemné korelace mezi různými signály se automaticky počítají jako klíčové faktory. To vám pomůže aktivně chránit komplexní systémy před chybami.

Pomocí klientské knihovny lineární detektoru anomálií pro JavaScript:

* Zjišťuje anomálie na úrovni systému ze skupiny časových řad.
* Když kterákoli z jednotlivých časových řad neřekne hodně a musíte se podívat na všechny signály a zjistit problém.
* Predicative údržbu drahých fyzických prostředků s desítkou až stovkami různých typů senzorů, které měří různé aspekty stavu systému.

[Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem. 

```console
npm init
```

Vytvořte soubor s názvem `index.js` a importujte následující knihovny: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Vytvořte proměnné koncového bodu a klíče Azure prostředku. Vytvořte další proměnnou pro ukázkový datový soubor.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Aby bylo možné používat rozhraní API pro detekci anomálií lineární, musíme před použitím detekce vyškolit náš vlastní model. Data použitá pro školení jsou dávkou časových řad, přičemž každá časová řada by měla být ve formátu CSV se dvěma sloupci, časovým razítkem a hodnotou. Všechny časové řady by měly být komprimované do jednoho souboru zip a nahrály do [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md). Ve výchozím nastavení se název souboru použije k reprezentaci proměnné pro časovou řadu. Alternativně je možné do souboru ZIP zahrnout další meta.js, pokud chcete, aby název proměnné byl jiný než název souboru. zip. Po vygenerování [adresy URL (SAS) objektu BLOB (Shared Access signatury)](../../../../storage/common/storage-sas-overview.md)můžeme pro školení použít adresu URL souboru ZIP.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ms-rest-azure` balíčky a `azure-ai-anomalydetector` npm. Knihovna CSV-Parse se používá také v tomto rychlém startu:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro detekci anomálií pro Node.js:

* [Ověření klienta](#authenticate-the-client)
* [Učení modelu](#train-a-model)
* [Detekovat anomálie](#detect-anomalies)
* [Exportovat model](#export-model)
* [Odstranit model](#delete-model)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `AnomalyDetectorClient` objektu s vaším koncovým bodem a přihlašovacími údaji.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Učení modelu

### <a name="construct-a-model-result"></a>Sestavení výsledku modelu

Nejdřív musíme vytvořit požadavek na model. Ujistěte se, že počáteční a koncový čas se zarovnává se zdrojem dat.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Výuka nového modelu

Požadavek na model bude nutné předat metodě klienta anomálie detektoru `trainMultivariateModel` .

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Pokud chcete zjistit, jestli je školení modelu hotové, můžete sledovat stav modelu:

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Detekovat anomálie

Pomocí `detectAnomaly` funkcí a `getDectectionResult` určíte, jestli ve zdroji dat nejsou nějaké anomálie.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>Exportovat model

Pokud chcete vyexportovat vyškolený model, použijte `exportModel` funkci.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Odstranit model

Pokud chcete odstranit existující model, který je k dispozici pro aktuální prostředek, použijte `deleteMultivariateModel` funkci.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

## <a name="next-steps"></a>Další kroky

* [Osvědčené postupy pro detekci anomálií lineární](../../concepts/best-practices-multivariate.md)
