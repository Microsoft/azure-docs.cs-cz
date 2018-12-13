---
title: Text sloučení kognitivního vyhledávání dovednosti – Azure Search
description: Sloučení textu z kolekce polí do jednoho konsolidované pole. Pomocí této kognitivních dovedností v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: b29d32d39b4efb7e242a3ae3213512798622d1e9
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314512"
---
#    <a name="text-merge-cognitive-skill"></a>Kognitivní dovednosti sloučení textu

**Sloučení textu** dovednosti konsoliduje text z kolekce polí do jednoho pole. 

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| insertPreTag  | Řetězec, které se mají zahrnout před každou vložení. Výchozí hodnota je `" "`. Pokud chcete vynechat, nechte pole, nastavte hodnotu na `""`.  |
| insertPostTag | Řetězce mají být zahrnuty po každé vložení. Výchozí hodnota je `" "`. Pokud chcete vynechat, nechte pole, nastavte hodnotu na `""`.  |


##  <a name="sample-input"></a>Ukázkový vstup
Dokument JSON poskytuje použitelné vstup pro tuto dovednost může být:

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
Tento příklad ukazuje výstup předchozího vstup předpokladu, že *insertPreTag* je nastavena na `" "`, a *insertPostTag* je nastavena na `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Rozšířené ukázková definice dovedností

Běžný scénář použití sloučení textu je sloučit textovou reprezentaci řetězce obrázků (text ze OCR dovedností nebo popisek image) do pole obsahu dokumentu. 

Následující příklad dovednosti používá dovednosti optické rozpoznávání znaků k extrakci textu z imagí vloží do dokumentu. V dalším kroku se vytvoří *merged_text* pole tak, aby obsahovala původní i OCRed text z každé image. 

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
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
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
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Výše uvedený příklad předpokládá, že existuje pole normalized bitové kopie. Chcete-li získat pole normalized imagí, nastavte *imageAction* konfigurace definice indexeru pro *generateNormalizedImages* jak je znázorněno níže:

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

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
