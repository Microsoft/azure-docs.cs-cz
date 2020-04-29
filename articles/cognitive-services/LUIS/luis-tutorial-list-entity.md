---
title: Extact text odpovídá entitám – LUIS
titleSuffix: Azure Cognitive Services
description: Naučte se, jak přidat entitu seznamu, která vám pomůže LUIS variace popisku slova nebo fráze.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73499480"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Použití entity list ke zvýšení detekce entit 
Tento článek ukazuje použití [entity seznam](luis-concept-entity-types.md) k zvýšení detekce entit. Entity seznamu nemusejí být označeny, protože se jedná o přesnou shodu podmínek.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit entitu seznamu 
> * Přidat normalizované hodnoty a synonyma
> * Ověřit vylepšenou identifikaci entity

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Poslední [Node. js](https://nodejs.org)
> * [HomeAutomation aplikace Luis](luis-get-started-create-app.md). Pokud nemáte vytvořenou aplikaci pro domácí automatizaci, vytvořte novou aplikaci a přidejte předem sestavenou doménu **HomeAutomation**. Trénujte a publikujte aplikaci. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (Pokud se dotazuje mnohokrát), ID aplikace, ID verze a [oblast](luis-reference-regions.md) pro aplikaci Luis.

> [!Tip]
> Pokud ještě předplatné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Veškerý kód v tomto článku je k dispozici v [úložišti GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Použití aplikace HomeAutomation
Aplikace HomeAutomation poskytuje kontrolu nad zařízeními, jako jsou například světla, zábavní systémy a ovládací prvky prostředí, jako je například vytápění a chlazení. Tyto systémy mají několik různých názvů, které mohou obsahovat názvy výrobců, přezdívky, akronymy a slangem. 

Jeden systém, který má mnoho názvů v různých jazykových verzích a demografických údajích, je termostat. Termostat může řídit chlazení i systémy vytápění pro dům nebo budova.

V ideálním případě by se měl následující projevy přeložit na předem sestavenou entitu **HomeAutomation. zařízení**:

|#|utterance|identifikovaná entita|skóre|
|--|--|--|--|
|1|zapnout AC|HomeAutomation. Device – "AC"|0,8748562|
|2|vypnutí tepla|HomeAutomation. Device – "tepla"|0,784990132|
|3|zajištění jeho chlazení|||

První dvě projevy se mapují na různá zařízení. Třetí utterance, "vychladit IT", nemapuje na zařízení, ale místo toho požaduje výsledek. LUIS neví, že výraz "studená" znamená, že je termostatem požadované zařízení. V ideálním případě by LUIS měla vyřešit všechna tato projevy na stejné zařízení. 

## <a name="use-a-list-entity"></a>Použít entitu seznam
Entita HomeAutomation. Device je skvělé pro malý počet zařízení nebo s malým počtem jejich názvů. V případě stavebních kancelářských nebo areálů se názvy zařízení zvětšují nad rámec užitečnosti entity HomeAutomation. Device. 

**Entita seznamu** je pro tento scénář vhodná, protože sada podmínek pro zařízení v budově nebo areálu je známá sada, a to i v případě, že se jedná o velmi velkou sadu. Pomocí seznamu entit může LUIS získat jakoukoli možnou hodnotu v sadě pro termostata a vyřešit ji do pouze jednoho zařízení "termostatu". 

V tomto článku se vytvoří seznam entit s termostatem. Alternativní názvy pro termostat v tomto článku jsou: 

|alternativní názvy pro termostat|
|--|
| proud |
| a/c|
| a-c|
|ohřívač|
|provozu|
|hotter|
|chladírenský|
|studená|

Pokud LUIS potřebuje určit novou alternativu často, je [seznam frází](luis-concept-feature.md#how-to-use-phrase-lists) lepší odpověď.

## <a name="create-a-list-entity"></a>Vytvořit entitu seznamu
Vytvořte soubor Node. js a zkopírujte do něj následující kód. Změňte hodnoty authoringKey, appId, versionId a region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Pomocí následujícího příkazu nainstalujte závislosti NPM a spusťte kód pro vytvoření entity seznamu:

```console
npm install && node add-entity-list.js
```

Výstupem běhu je ID entity seznamu:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Trénování modelu
Výukové LUIS, aby nový seznam ovlivnil výsledky dotazu. Školení je proces školení se dvěma částmi a pak zkontroluje stav, pokud je školení hotové. Výuka aplikace s mnoha modely může chvíli trvat. Následující kód naloží aplikaci a počká, až bude školení úspěšné. Kód používá strategii čekání a opakování, aby nedošlo k chybě 429 "příliš mnoho požadavků". 

Vytvořte soubor Node. js a zkopírujte do něj následující kód. Změňte hodnoty authoringKey, appId, versionId a region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Pomocí následujícího příkazu spusťte kód pro výuku aplikace:

```console
node train.js
```

Výstupem běhu je stav každé iterace kurzů LUIS modelů. Následující spuštění vyžadovalo pouze jednu kontrolu školení:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publikování modelu
Publikování, aby byla entita seznamu dostupná z koncového bodu.

Vytvořte soubor Node. js a zkopírujte do něj následující kód. Změňte hodnoty endpointKey, appId a region. AuthoringKey můžete použít, pokud neplánujete tento soubor volat nad rámec vaší kvóty.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Pomocí následujícího příkazu spusťte kód pro dotazování aplikace:

```console
node publish.js
```

Následující výstup obsahuje adresu URL koncového bodu pro jakékoli dotazy. Skutečné výsledky JSON by zahrnovaly reálné appID. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Dotazování aplikace 
Dotazování aplikace z koncového bodu na důkaz, že entita seznamu pomůže LUIS určit typ zařízení.

Vytvořte soubor Node. js a zkopírujte do něj následující kód. Změňte hodnoty endpointKey, appId a region. AuthoringKey můžete použít, pokud neplánujete tento soubor volat nad rámec vaší kvóty.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Pro spuštění kódu a dotazování aplikace použijte následující příkaz:

```console
node train.js
```

Výstupem jsou výsledky dotazu. Vzhledem k tomu, že kód přidal do řetězce dotazu **podrobnou** dvojici název-hodnota, zahrnuje výstup všechny záměry a jejich skóre:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Konkrétní zařízení **termostatu** je označeno výsledným dotazem "zahříváním". Vzhledem k tomu, že původní entita HomeAutomation. Device je stále v aplikaci, můžete zobrazit také její výsledky. 

Zkuste další dvě projevy a podívejte se, že se také vrátí jako termostat. 

|#|utterance|entita|type|value|
|--|--|--|--|--|
|1|zapnout AC| proud | DevicesList | Termostat|
|2|vypnutí tepla|hork| DevicesList |Termostat|
|3|zajištění jeho chlazení|studená|DevicesList|Termostat|

## <a name="next-steps"></a>Další kroky

Můžete vytvořit jinou entitu seznamu pro rozšíření umístění zařízení do místností, podlah nebo budov. 
