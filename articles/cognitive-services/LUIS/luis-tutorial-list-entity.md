---
title: Entity shody textu Extact – LUIS
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak přidat entitu seznamu, která pomáhá luis označit varianty slova nebo fráze.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499480"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Použití entity seznamu ke zvýšení zjišťování entity 
Tento článek ukazuje použití [entity seznamu](luis-concept-entity-types.md) ke zvýšení detekce entity. Entity seznamu nemusí být označeny, protože se přesně shodují s podmínkami.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření entity seznamu 
> * Přidání normalizovaných hodnot a synonym
> * Ověřit vylepšenou identifikaci entity

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Nejnovější [soubor Node.js](https://nodejs.org)
> * [Aplikace HomeAutomation LUIS](luis-get-started-create-app.md). Pokud nemáte vytvořenou aplikaci Domácí automatizace, vytvořte novou aplikaci a přidejte předestavěnou doménu **HomeAutomation**. Trénujte a publikujte aplikaci. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (pokud dotazování mnohokrát), ID aplikace, ID verze a [oblast](luis-reference-regions.md) pro aplikaci LUIS.

> [!Tip]
> Pokud ještě nemáte předplatné, můžete se zaregistrovat k [bezplatnému účtu](https://azure.microsoft.com/free/).

Veškerý kód v tomto článku je k dispozici v [úložišti GitHub u vzorcích Azure](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Použití aplikace HomeAutomation
Aplikace HomeAutomation vám poskytuje kontrolu nad zařízeními, jako jsou světla, zábavní systémy a ovládací prvky prostředí, jako je vytápění a chlazení. Tyto systémy mají několik různých názvů, které mohou zahrnovat názvy výrobců, přezdívky, zkratky a slang. 

Jeden systém, který má mnoho jmen v různých kulturách a demografii je termostat. Termostat může řídit jak chladicí, tak topné systémy pro dům nebo budovu.

V ideálním případě by následující projevy by měly vyřešit předdefinované entity **HomeAutomation.Device**:

|#|Promluva|identifikovaný subjekt|skóre|
|--|--|--|--|
|1|zapněte ac|HomeAutomation.Device - "ac"|0.8748562|
|2|zapněte teplo|HomeAutomation.Device - "teplo"|0.784990132|
|3|aby bylo chladnější|||

První dva projevy mapovat na různá zařízení. Třetí utterance, "aby bylo chladnější", není mapovat na zařízení, ale místo toho požaduje výsledek. Služba LUIS neví, že termín "chladnější", znamená, že termostat je požadované zařízení. V ideálním případě by služba LUIS měla vyřešit všechny tyto projevy do stejného zařízení. 

## <a name="use-a-list-entity"></a>Použití entity seznamu
Entita HomeAutomation.Device je skvělá pro malý počet zařízení nebo s několika variantami názvů. Pro kancelářskou budovu nebo kampus se názvy zařízení zvětšují nad rámec užitečnosti entity HomeAutomation.Device. 

**Entita seznamu** je dobrou volbou pro tento scénář, protože sada podmínek pro zařízení v budově nebo areálu školy je známá sada, i když se jedná o obrovskou sadu. Pomocí entity seznamu luis můžete přijímat všechny možné hodnoty v sadě pro termostat a vyřešit ji pouze na jedno zařízení "termostat". 

Tento článek vytvoří seznam entit s termostatem. Alternativní názvy termostatu v tomto článku jsou: 

|alternativní názvy termostatu|
|--|
| Ac |
| A/c|
| a-c|
|Ohřívač|
|Horké|
|Teplejší|
|Studené|
|Chladnější|

Pokud luis potřebuje určit novou alternativu často, pak [seznam frází](luis-concept-feature.md#how-to-use-phrase-lists) je lepší odpověď.

## <a name="create-a-list-entity"></a>Vytvoření entity seznamu
Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty authoringKey, appId, versionId a region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Pomocí následujícího příkazu nainstalujte závislosti NPM a spusťte kód k vytvoření entity seznamu:

```console
npm install && node add-entity-list.js
```

Výstupem spuštění je ID entity seznamu:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Trénování modelu
Trénování služby LUIS v pořadí, aby nový seznam ovlivnil výsledky dotazu. Školení je dvoudílný proces školení, pak kontrola stavu, pokud školení je hotovo. Aplikace s mnoha modely může trvat několik okamžiků trénovat. Následující kód trénuje aplikace pak čeká, dokud školení je úspěšný. Kód používá strategii čekání a opakování, aby se zabránilo chybě 429 "Příliš mnoho požadavků". 

Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty authoringKey, appId, versionId a region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Pomocí následujícího příkazu spusťte kód pro trénování aplikace:

```console
node train.js
```

Výstup spuštění je stav každé iterace školení modelů LUIS. Následující provedení vyžaduje pouze jednu kontrolu školení:

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
Publikujte tak, aby entita seznamu byla k dispozici z koncového bodu.

Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty endpointKey, appId a region. Pokud neplánujete volat tento soubor nad rámec maximální kvóty, můžete použít klíč authoringKey.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Ke spuštění kódu pro dotazování aplikace použijte následující příkaz:

```console
node publish.js
```

Následující výstup obsahuje adresu URL koncového bodu pro všechny dotazy. Skutečné výsledky JSON by zahrnovaly skutečné appID. 

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

## <a name="query-the-app"></a>Dotaz na aplikaci 
Dotaz aplikace z koncového bodu k prokázání, že entita seznamu pomáhá LUIS určit typ zařízení.

Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty endpointKey, appId a region. Pokud neplánujete volat tento soubor nad rámec maximální kvóty, můžete použít klíč authoringKey.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Pomocí následujícího příkazu spusťte kód a dotazujte se na aplikaci:

```console
node train.js
```

Výstupem jsou výsledky dotazu. Vzhledem k tomu, že kód přidal **podrobný** pár názvu/hodnoty do řetězce dotazu, výstup zahrnuje všechny záměry a jejich skóre:

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

Konkrétní zařízení **termostatu** je identifikováno dotazem "zapněte teplo". Vzhledem k tomu, že původní entita HomeAutomation.Device je stále v aplikaci, můžete také zobrazit její výsledky. 

Zkuste další dva projevy vidět, že jsou také vráceny jako termostat. 

|#|Promluva|entita|type|value|
|--|--|--|--|--|
|1|zapněte ac| Ac | Seznam zařízení | Termostat|
|2|zapněte teplo|Tepla| Seznam zařízení |Termostat|
|3|aby bylo chladnější|Chladnější|Seznam zařízení|Termostat|

## <a name="next-steps"></a>Další kroky

Můžete vytvořit další entitu Seznam a rozšířit umístění zařízení na místnosti, podlahy nebo budovy. 
