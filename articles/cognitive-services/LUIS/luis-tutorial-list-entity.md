---
title: Extact textu shody entity
description: Zjistěte, jak přidat entitu seznamu umožňující LUIS popisek variace slova nebo fráze.
services: cognitive-services
author: diberry
titleSuffix: Azure
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: diberry
ms.openlocfilehash: be2c7aea356f6d7b73438499b7e4d680ed4bad7c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259235"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Zvyšte zjišťování entit pomocí seznamu entit 
Tento kurz ukazuje použití [seznam entit](luis-concept-entity-types.md) zvýšit zjišťování entit. Seznam entit není nutné popisek, protože jde o přesnou shodu podmínek.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Vytvoření seznamu entit 
* Přidat normalizované hodnoty a synonyma
* Ověření identifikace vylepšené entity

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Nejnovější [Node.js](https://nodejs.org)
> * [Aplikace LUIS HomeAutomation](luis-get-started-create-app.md). Pokud nemáte aplikaci Domů Automation vytvořili, vytvořte novou aplikaci a přidejte předem připravených domény **HomeAutomation**. Trénujte a publikujte aplikaci. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (je-li dotazování v mnoha případech), ID aplikace, ID verze a [oblasti](luis-reference-regions.md) aplikace LUIS.

> [!Tip]
> Pokud ještě nemáte předplatné, si můžete zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Veškerý kód v tomto kurzu je k dispozici na [úložiště GitHub LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Používání HomeAutomation aplikace
Umožňuje aplikaci HomeAutomation například zahřívání a vychladnutí ovládací prvky řízení zařízení, například světla, systémy pro zábavu a prostředí. Tyto systémy obsahovat několik jiné názvy, které mohou obsahovat názvy, přezdívky, zkratky a slangem výrobce. 

Systémů, který má mnoho názvů přes různé jazykové verze a demografických údajů je termostat. Termostat můžete řídit chlazení i vytápění pro domácnosti nebo v budově.

V ideálním případě by měla vyřešit následující projevy na předem připravených entit **HomeAutomation.Device**:

|#|Utterance|entitu identifikovanou|skóre|
|--|--|--|--|
|1|zapnout ac|HomeAutomation.Device – "ac"|0.8748562|
|2|objevit tepla|HomeAutomation.Device – "heat"|0.784990132|
|3|Zkontrolujte nižší|||

První dva projevy namapovat na různých zařízeních. Třetí utterance "usnadňují nižší", není namapován na zařízení ale vyžádá výsledek. Služba LUIS neví, že termín "nižší", znamená to, že termostat požadované zařízení. V ideálním případě LUIS by měla vyřešit všechny tyto projevy do stejného zařízení. 

## <a name="use-a-list-entity"></a>Použijte seznam entit
HomeAutomation.Device entity se skvěle hodí pro malý počet zařízení nebo pomocí několika variant názvy. Kancelářská budova nebo areálu názvy zařízení nárůst užitečnost HomeAutomation.Device entity. 

A **seznam entit** je dobrou volbou pro tento scénář, protože sadu podmínek pro zařízení v budově nebo areálu je sada známých i v případě, že je velmi velké sady. Pomocí seznamu entit může LUIS přijímat všechny možné hodnoty v sadě pro termostat a vyřešit dolů jedno zařízení "termostat". 

V tomto kurzu bude vytvoření seznamu entit pomocí termostat. Alternativní názvy pro termostat v tomto kurzu jsou následující: 

|alternativní názvy pro termostat|
|--|
| ac |
| z účtu|
| -c|
|Ohřívač|
|za běhu|
|hotter|
|studené|
|nižší|

Pokud je potřeba určit nový alternativní často, LUIS o [seznam frází](luis-concept-feature.md#how-to-use-phrase-lists) je lepší odpověď.

## <a name="create-a-list-entity"></a>Vytvoření seznamu entit
Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty authoringKey, appId, versionId a oblast.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Závislosti na NPM nainstalovat a spustit kód, který vytvoří seznam entit, použijte následující příkaz:

```console
npm install && node add-entity-list.js
```

Výstup spuštění je ID entity seznamu:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Trénování modelu
Trénování služby LUIS v pořadí pro nový seznam ovlivnit výsledky dotazu. Školení je proces dvojdílného školení, pak kontrola stavu, pokud se provádí na školení. Aplikace s více modely může trvat několik minut pro trénování. Následující kód trénovat aplikace pak počká, dokud neproběhne školení. Tento kód použije strategie opakování a počkejte, aby 429 "příliš mnoho požadavků" Chyba. 

Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty authoringKey, appId, versionId a oblast.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Použijte následující příkaz pro spuštění kódu pro trénování aplikace:

```console
node train.js
```

Výstup spuštění je stav každé iteraci trénování modelů služby LUIS. Následující spuštění vyžaduje pouze jedna kontrola školení:

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
Publikujte seznam entit je dostupné z koncového bodu.

Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty endpointKey appId a oblast. Vaše authoringKey můžete použít, pokud nemáte v plánu pro tento soubor mimo maximální kvóty volání.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Použijte následující příkaz pro spuštění kódu k dotazování aplikace:

```console
node publish.js
```

Následující výstup zahrnuje adresu url koncového bodu pro všechny dotazy. Skutečné výsledky JSON by obsahovat skutečné ID aplikace. 

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

## <a name="query-the-app"></a>Dotaz aplikace 
Dotaz na aplikace z koncového bodu prokázat, že seznam entit přispívá k LUIS určit typ zařízení.

Vytvořte soubor Node.js a zkopírujte do něj následující kód. Změňte hodnoty endpointKey appId a oblast. Vaše authoringKey můžete použít, pokud nemáte v plánu pro tento soubor mimo maximální kvóty volání.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Použijte následující příkaz pro spuštění kódu a dotazování aplikací:

```console
node train.js
```

Výstup se výsledky dotazu. Protože kód přidá **podrobné** dvojice název/hodnota řetězce dotazu, výstup zahrnuje veškeré záměry a jejich výsledky:

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

Konkrétní zařízení **termostat** se určuje podle orientované na výsledek dotazu "zapnutí nahoru heat". Protože původní entita HomeAutomation.Device je stále v aplikaci, zobrazí se i jeho výsledky. 

Vyzkoušejte další dva projevy zobrazíte, že jsou také vrácena jako termostat. 

|#|Utterance|entita|type|hodnota|
|--|--|--|--|--|
|1|zapnout ac| ac | DevicesList | Termostat|
|2|objevit tepla|tepla| DevicesList |Termostat|
|3|Zkontrolujte nižší|nižší|DevicesList|Termostat|

## <a name="next-steps"></a>Další postup

Můžete vytvořit jinou entitou seznamu Rozbalit umístění zařízení do místnosti, podlažích nebo budovy. 
