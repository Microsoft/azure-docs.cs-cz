---
title: Kognitivní dovednosti sloučení textu
titleSuffix: Azure Cognitive Search
description: Sloučit text z kolekce polí do jednoho sloučeného pole. Pomocí této kognitivní dovednosti v kanálu obohacení ai v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162340"
---
#   <a name="text-merge-cognitive-skill"></a>Kognitivní dovednosti sloučení textu

Dovednost **Sloučení textu** sloučí text ze sbírky polí do jednoho pole. 

> [!NOTE]
> Tato dovednost není vázána na rozhraní API služby Cognitive Services a za jeho použití se vám neúčtují poplatky. Stále byste měli [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste však přepsali možnost **Volný** prostředek, který vás omezuje na malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| insertPreTag  | Řetězec, který má být zahrnut před každým vložením. Výchozí hodnota je `" "`. Chcete-li místo vynechat, nastavte `""`hodnotu na .  |
| insertPostTag | Řetězec, který má být zahrnut po každém vložení. Výchozí hodnota je `" "`. Chcete-li místo vynechat, nastavte `""`hodnotu na .  |


##  <a name="sample-input"></a>Vstup vzorku
Dokument JSON poskytující použitelný vstup pro tuto dovednost může být:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Ukázkový výstup
Tento příklad ukazuje výstup předchozího vstupu, za předpokladu, `" "`že *insertPreTag* je nastavena na , a *insertPostTag* je nastavena na `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Rozšířená definice sady dovedností vzorku

Běžným scénářem pro použití sloučení textu je sloučení textové reprezentace obrazů (text z dovednosti rozpoznávání OCR nebo titulek obrázku) do pole obsahu dokumentu. 

Následující příklad skillset používá dovednost OCR extrahovat text z obrazů vložených do dokumentu. Dále vytvoří *pole merged_text,* které bude obsahovat původní text i text OCRed z každého obrázku. Další informace o dovednostech ocr naleznete [zde](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad předpokládá, že existuje pole normalizované obrázky. Chcete-li získat pole normalizované obrázky, nastavte konfiguraci *imageAction* v definici indexeru tak, aby *generovala Normalizované obrázky,* jak je znázorněno níže:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
