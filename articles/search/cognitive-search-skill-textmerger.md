---
title: Dovednost pro vnímání textu sloučení
titleSuffix: Azure Cognitive Search
description: Sloučí text z kolekce polí do jednoho konsolidovaného pole. Využijte tuto funkci rozpoznávání v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c44f37c37bff2ddeb0fbba83d170054bf21129a6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791842"
---
#   <a name="text-merge-cognitive-skill"></a>Dovednost pro vnímání textu sloučení

Dovednost **sloučení textu** slučuje text z kolekce polí do jednoho pole. 

> [!NOTE]
> Tato dovednost není vázaná na rozhraní Cognitive Services API a neúčtují se za jejich použití. K dispozici byste ale měli i [prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste mohli přepsat možnost **bezplatného** prostředku, která omezuje na malý počet denních rozšíření za den.

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. MergeSkill

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| insertPreTag  | Řetězec, který má být zahrnut před každým vložením. Výchozí hodnota je `" "`. Chcete-li vynechat místo, nastavte hodnotu na `""`.  |
| insertPostTag | Řetězec, který má být zahrnut po každém vložení. Výchozí hodnota je `" "`. Chcete-li vynechat místo, nastavte hodnotu na `""`.  |


##  <a name="sample-input"></a>Vzorový vstup
Dokument JSON, který poskytuje použitelný vstup pro tuto dovednost, může být:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28],
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Ukázkový výstup
Tento příklad ukazuje výstup předchozí vstupní hodnoty za předpokladu, že je *insertPreTag* nastaven na hodnotu `" "`a *insertPostTag* je nastaven na hodnotu `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Rozšířená ukázka definice dovednosti

Běžným scénářem použití sloučení textu je sloučení textové reprezentace obrázků (text z dovednosti OCR nebo titulku obrázku) do pole obsah dokumentu. 

Následující příklad dovednosti používá dovednost optického rozpoznávání znaků k extrakci textu z obrázků vložených v dokumentu. V dalším kroku se vytvoří pole *merged_text* , které bude obsahovat původní i OCRed text z každého obrázku. Další informace o dovednostech OCR najdete [tady](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

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
Výše uvedený příklad předpokládá, že existuje pole Normalized-images. Chcete-li získat pole Normalized-images, nastavte v definici indexeru konfiguraci *imageAction* na *generateNormalizedImages* , jak je znázorněno níže:

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

## <a name="see-also"></a>Další informace najdete v tématech

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
