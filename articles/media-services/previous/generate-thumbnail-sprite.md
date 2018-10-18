---
title: Generování miniatur sprite pomocí Azure Media Services | Dokumentace Microsoftu
description: Toto téma ukazuje, jak generovat miniatury sprite pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/24/2018
ms.author: juliako
ms.openlocfilehash: f9ad7fcf414e90acc40ee5cd42e322a3b9e47f17
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380997"
---
# <a name="generate-a-thumbnail-sprite"></a>Generování miniatur sprite 

Media Encoder Standard můžete použít ke generování miniatur sprite, což je soubor ve formátu JPEG, který obsahuje více miniatur malé rozlišení spojených dohromady do jedné image (velké) společně s VTT souboru. Tento soubor VTT Určuje časový rozsah v vstupního videa, která představuje jednotlivé miniatury, společně s velikostí a souřadnice této miniaturu v rámci velkých souborů ve formátu JPEG. Přehrávačů videa pomocí image Souborová služba a sprite VTT zobrazit "visual" seekbar prohlížeč zobrazovaly vizuální zpětnou vazbu, když scrubbingu zpět a vpřed podél časové osy videa.

Chcete-li použít k vygenerování Thumbnail Sprite, přednastavený kontext kodéru Media Encoder Standard:

1. Musíte použít formát JPG obrázek miniatury
2. Musíte zadat Start/krok/rozsah hodnot jako buď časová razítka nebo hodnoty % (a ne počty rámce) 
    
    1. Je možné kombinovat časová razítka a % hodnoty

3. Musí mít hodnotu SpriteColumn jako nezáporné číslo větší než nebo rovno 1

    1. Pokud SpriteColumn nastavená na M > = 1, obdélník se sloupci M je výstupní image. Pokud počet miniatur vygenerovanou přes #2 není násobkem M, poslední řádek bude nekompletní a levém s černým pixelů.  

Zde naleznete příklad:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Známé problémy

1.  Není možné vygenerovat sprite image s jeden řádek obrázků (SpriteColumn = 1 výsledky do image s jedním sloupcem).
2.  Bloků sprite imagí do středně velkých obrázků JPEG se ještě nepodporuje. Proto musí být věnovat pozornost omezit počet miniatur a jejich velikost tak, aby výsledný stitched Sprite miniaturu po 8 pixelech M nebo i rychleji.
3.  Azure Media Player podporuje prvky, které budou v prohlížeči Edge, Chrome a Firefox. Analýza VTT není podporována v IE11.

## <a name="next-steps"></a>Další postup

[Kódování obsahu](media-services-encode-asset.md)