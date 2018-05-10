---
title: Text sloučení kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Sloučí text z kolekce pole do jednoho pole konsolidované. Pomocí této kognitivní znalostí v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: e288748d7433f4b3c7da7db1ab1ef2ee487318df
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
#    <a name="text-merge-cognitive-skill"></a>Text sloučení kognitivní dovedností

**Text sloučení** odborností konsoliduje text z kolekce pole do jednoho pole. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| insertPreTag  | Řetězec má být vložen před každou vložení. Výchozí hodnota je `" "`. Vynechat prostor, nastavte hodnotu na `""`.  |
| insertPostTag | Řetězec, které mají být zahrnuty po každé vložení. Výchozí hodnota je `" "`. Vynechat prostor, nastavte hodnotu na `""`.  |


##  <a name="sample-input"></a>Ukázka vstup
Dokument JSON poskytuje použitelné vstup pro tento odborností může být:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Ukázkový výstup
Tento příklad ukazuje výstup předchozí vstupu, za předpokladu, který *insertPreTag* je nastaven na `" "`, a *insertPostTag* je nastaven na `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Definice skillset rozšířené ukázka

Běžný scénář pro pomocí možnosti sloučení textu je sloučit textovou reprezentaci bitových kopií (text ze znalostí rozpoznávání znaků nebo popisek bitové kopie) do pole obsahu dokumentu. 

Následující příklad skillset rozbalte text z Image vložené v dokumentu pomocí dovedností rozpoznávání znaků. V dalším kroku vytvoří *merged_text* pole tak, aby obsahovala původní i OCRed text z každé bitové kopie. 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
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
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad předpokládá, že pole normalized-bitové kopie existuje. Chcete-li získat pole normalized-bitové kopie, nastavte *imageAction* konfigurace v indexeru definici *generateNormalizedImages* jak je uvedeno níže:

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

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)