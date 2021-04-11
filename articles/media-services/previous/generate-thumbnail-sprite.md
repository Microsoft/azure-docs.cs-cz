---
title: Generování Sprite miniatury pomocí Azure Media Services | Microsoft Docs
description: Toto téma ukazuje, jak vygenerovat Sprite miniatury pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce66b6f605b10f65ec8a98d14c682928c7b21321
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012237"
---
# <a name="generate-a-thumbnail-sprite"></a>Vytvoření spritu miniatury

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Můžete použít Media Encoder Standard k vygenerování Sprite miniatury, což je soubor JPEG, který obsahuje několik miniatur malých rozlišení dohromady propojených s jednou (velkou) imagí společně se souborem VTT. Tento soubor VTT určuje časový rozsah ve vstupním videu, které každá miniatura představuje, spolu s velikostí a souřadnicemi této miniatury v rámci velkého souboru JPEG. Hráči videa používají soubor VTT a Sprite k zobrazení příznaku "vizuálu", který poskytuje čtenářům vizuální zpětnou vazbu při čištění a předávání podél časové osy videa.

Aby bylo možné použít Media Encoder Standard k vygenerování Sprite miniatur, předvolba:

1. Je nutné použít formát obrázku miniatury JPG.
2. Je nutné zadat hodnoty počáteční/krok/rozsah buď jako časová razítka, nebo% hodnot (a nikoli počtu snímků). 
    
    1. Je možné kombinovat časová razítka a hodnoty (% Values).

3. Musí mít hodnotu SpriteColumn jako nezáporné číslo, které je větší nebo rovno 1

    1. Pokud je SpriteColumn nastavené na M >= 1, výstupní obrázek je obdélník se sloupci M. Pokud počet miniatur vygenerovaných pomocí #2 není přesný násobek M, poslední řádek bude neúplný a vlevo černých pixelů.  

Tady je příklad:

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

1.  Není možné vygenerovat obrázek Sprite s jedním řádkem obrázků (SpriteColumn = 1, výsledkem je obrázek s jedním sloupcem).
2.  Zablokování imagí Sprite do střední velikosti imagí JPEG ještě není podporováno. Proto je třeba dbát na to, aby se omezil počet miniatur a jejich velikost, aby výsledný Pohyblivý Sprit miniatur byl okolo 8 min pixelů nebo méně.
3.  Azure Media Player podporuje Sprite v prohlížečích Microsoft Edge, Chrome a Firefox. Analýza VTT není v IE11 podporovaná.

## <a name="next-steps"></a>Další kroky

[Kódování obsahu](media-services-encode-asset.md)
