---
title: Označení entity automaticky seznamu entitu pomocí Nodejs | Microsoft Docs
description: Informace o postupu přidání entity seznamu pomohou LEOŠ popisek variace slovo nebo frázi.
services: cognitive-services
author: v-geberr
titleSuffix: Azure
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: e8558ecf4a64dbccef6e6367c1447bdcdb005126
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "35342496"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Pomocí seznamu entity zvýšit detekce entity 
Tento kurz ukazuje použití [seznamu entity](luis-concept-entity-types.md) zvýšit detekce entity. Seznam entity není potřeba s názvem bez přípony, jako jsou přesnou shodu podmínek.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Vytvoření seznamu entit 
* Přidat normalizovaný hodnoty a synonyma.
* Ověření identifikace vylepšené entity

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Nejnovější [Node.js](https://nodejs.org)
> * [Aplikace LEOŠ HomeAutomation](luis-get-started-create-app.md). Pokud jste vytvořili aplikaci Domů automatizace, vytvořte novou aplikaci a přidejte předem domény **HomeAutomation**. Trénování a publikování aplikace. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (Pokud dotazování mnohokrát), ID aplikace, ID verze, a [oblast](luis-reference-regions.md) LEOŠ aplikace.

> [!Tip]
> Pokud již nemáte předplatné, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Kód v tomto kurzu je k dispozici na [úložiště github LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Použití HomeAutomation aplikace
Poskytuje aplikaci HomeAutomation kontrolu nad zařízení, jako jsou indikátory, zábava systémy a prostředí ovládací prvky jako je například vytápění a chlazení. Tyto systémy obsahovat několik odlišné názvy, které mohou obsahovat názvy, přezdívky, režim a slang výrobce. 

Jeden systém, který má mnoho názvů v jiných jazykových verzí a demografie je termostatu. Termostatu může řídit chlazení i vytápění úklidové nebo sestavení.

V ideálním případě by měl směrovat následující utterances předem entity **HomeAutomation.Device**:

|#|utterance|entitu identifikovanou|skóre|
|--|--|--|--|
|1|zapnout ac|HomeAutomation.Device - "ac"|0.8748562|
|2|zapnout až heat|HomeAutomation.Device - "heat"|0.784990132|
|3|Zkontrolujte nižší|||

První dva utterances mapovat do různých zařízení. Třetí utterance "bylo nižší", není namapován na zařízení, ale místo toho požadavky výsledku. LEOŠ neví, že termín "nižší", znamená, že termostatu je požadovaná zařízení. V ideálním případě LEOŠ měli vyřešit všechny tyto utterances do stejného zařízení. 

## <a name="use-a-list-entity"></a>Pomocí seznamu entit
Entita HomeAutomation.Device je ideální pro malé množství zařízení nebo s několika rozdíly názvy. Kancelářská budova nebo kanceláře názvy zařízení nárůst užitečnost HomeAutomation.Device entity. 

A **seznamu entity** je vhodná pro tento scénář, protože sadu podmínek pro zařízení v budově nebo kanceláře se známou sadou, i když je velmi velké sady. Pomocí seznamu entity můžete LEOŠ přijímat žádné možnou hodnotu v sadě pro termostatu a přeložte ho dolů jedno zařízení "termostatu". 

V tomto kurzu bude vytvoření seznamu entity pomocí termostatu. Alternativní názvy pro termostatu v tomto kurzu jsou: 

|alternativní názvy pro termostatu|
|--|
| ac |
| z účtu|
| -c|
|topení|
|za běhu|
|hotter|
|uloží málo používaná data|
|nižší|

Pokud je potřeba určit nový alternativní často LEOŠ pak [frázi seznamu](luis-concept-feature.md#how-to-use-phrase-lists) je lepší odpověď.

## <a name="create-a-list-entity"></a>Vytvoření seznamu entit
Vytvořte soubor Node.js a zkopírujte do ní následující kód. Změňte hodnoty authoringKey, appId, versionId a oblast.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Závislosti NPM nainstalovat a spustit kód k vytvoření seznamu entity použijte následující příkaz:

```Javascript
npm install && node add-entity-list.js
```

Výstup spuštění, je ID entity seznamu:

```Javascript
026e92b3-4834-484f-8608-6114a83b03a6
```
## <a name="train-the-model"></a>Trénování modelu
Cvičení LEOŠ v pořadí pro nový seznam ovlivnit výsledky dotazu. Školení je proces dvě části školení, pak kontrola stavu, pokud se provádí školení. Aplikace s mnoha modely může chvíli trvat ke cvičení. Následující kód učí aplikace potom počká, dokud nebude úspěšná školení. Kód používá strategie počkejte a opakujte, aby se zabránilo 429 "příliš mnoho požadavků" Chyba. 

Vytvořte soubor Node.js a zkopírujte do ní následující kód. Změňte hodnoty authoringKey, appId, versionId a oblast.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Spustí kód pro učení aplikace použijte následující příkaz:

```Javascript
node train.js
```

Výstup spuštění, je stav každé iteraci školení LEOŠ modelů. Následující provádění vyžadována pouze jedna kontrola školení:

```Javascript
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
Publikujte tak entita seznamu je k dispozici z koncového bodu.

Vytvořte soubor Node.js a zkopírujte do ní následující kód. Změňte hodnoty endpointKey appId a oblast. Vaše authoringKey můžete použít, pokud neplánujete volání tento soubor překračuje limit kvóty.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Spustí kód k dotazování aplikace použijte následující příkaz:

```Javascript
node publish.js
```

Tento výstup obsahuje adresu url koncového bodu pro všechny dotazy. Skutečné výsledky JSON by obsahovat skutečné ID aplikace. 

```JSON
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
Dotaz aplikace z koncového bodu k prokázání, že entita seznamu pomáhá LEOŠ určit typ zařízení.

Vytvořte soubor Node.js a zkopírujte do ní následující kód. Změňte hodnoty endpointKey appId a oblast. Vaše authoringKey můžete použít, pokud neplánujete volání tento soubor překračuje limit kvóty.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Spuštění kódu a dotazovat se aplikace použijte následující příkaz:

```Javascript
node train.js
```

Výstup se výsledky dotazu. Protože kód přidána **podrobné** obsahuje dvojice název/hodnota do řetězce dotazu, výstup všech tříd Intent a jejich výsledky:

```JSON
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

Konkrétní zařízení, které **termostatu** přiřazen dotaz orientované na výsledek "zapnout až heat". Vzhledem k tomu, že původní entita HomeAutomation.Device je stále v aplikaci, můžete zobrazit také své výsledky. 

Zkuste další dva utterances zobrazíte, že jsou také vráceny jako termostatu. 

|#|utterance|entita|type|hodnota|
|--|--|--|--|--|
|1|zapnout ac| ac | DevicesList | Termostat|
|2|zapnout až heat|Heat| DevicesList |Termostat|
|3|Zkontrolujte nižší|nižší|DevicesList|Termostat|

## <a name="next-steps"></a>Další postup

Můžete vytvořit jinou entitou seznamu rozšiřovat umístění zařízení na místnosti, podlah nebo budovy. 
