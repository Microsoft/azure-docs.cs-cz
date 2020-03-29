---
title: Generování miniatur pomocí Mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak generovat miniatury sprite s Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61229046"
---
# <a name="generate-a-thumbnail-sprite"></a>Vytvoření spritu miniatury  

Pomocí standardu kodéru médií můžete použít ke generování miniatur, což je soubor JPEG, který obsahuje více malých miniatur rozlišení sešitých do jednoho (velkého) obrazu spolu se souborem VTT. Tento soubor VTT určuje časový rozsah ve vstupním videu, které představuje každá miniatura, spolu s velikostí a souřadnicemi této miniatury ve velkém souboru JPEG. Přehrávače videa používají soubor VTT a obrázek sprite k zobrazení "vizuálního" seekbaru, který poskytuje divákovi vizuální zpětnou vazbu při drhnutí zpět a vpřed podél časové osy videa.

Chcete-li ke generování miniatursprite použít standard kodéru médií, přednastavení:

1. Musí používat formát miniatury JPG
2. Musí určit hodnoty Start/Krok/Rozsah jako časová razítka nebo % hodnoty (a ne počty snímků) 
    
    1. Je v pořádku kombinovat časová razítka a hodnoty %

3. Musí mít hodnotu SpriteColumn jako nezáporné číslo větší nebo rovné 1.

    1. Pokud je SpriteColumn nastaven na M >= 1, výstupní obraz je obdélník se sloupci M. Pokud počet miniatur generovaných pomocí #2 není přesnýnásobek M, bude poslední řádek neúplný a zůstane s černými pixely.  

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

1.  Není možné generovat obrázek pohyblivého symbolu s jedním řádkem obrázků (SpriteColumn = 1 má za následek obrázek s jedním sloupcem).
2.  Nabloky obrazů pohyblivých symbolů do středně velkých obrazů JPEG ještě nejsou podporovány. Proto je třeba dbát na to, aby byl omezen počet miniatur a jejich velikost, aby výsledný sešívaný miniaturový skřítek byl kolem 8M pixelů nebo méně.
3.  Azure Media Player podporuje skřítky v prohlížečích Microsoft Edge, Chrome a Firefox. Analýza VTT není v ie11 podporována.

## <a name="next-steps"></a>Další kroky

[Kódování obsahu](media-services-encode-asset.md)
