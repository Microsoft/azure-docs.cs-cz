---
title: Zjišťování barevná schémata - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související se zjišťováním barevné schéma v obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d882ad89e68936d07ae4d76218c6e3ac450185a8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151324"
---
# <a name="detect-color-schemes-in-images"></a>Zjištění barevná schémata v obrázcích

Počítačové zpracování obrazu extrahuje barvy z obrázku. Barvy se analyzují pak ve třech různých kontextech: popředí dominantní barva, barvu pozadí dominantní a dominantní barvy pro bitovou kopii jako celek. Jsou seskupené do 12 dominantních doplňkových barev. Jsou to tyto doplňkové barvy: černá, modrá, hnědá, šedá, zelená, oranžová, růžová, fialová, červená, šedozelená, bílá a žlutá. Počítačové zpracování obrazu analyzuje extrahovat z obrázků se vraťte barvu motivu, který představuje nejvíce živý barvu na obrázku se divákům prostřednictvím kombinace dominantní barvy a sytost barev. Podle toho, jaké barvy jsou na obrázku, může být vrácena jednoduše černá a bílá nebo hexadecimální kódy doplňkových barev. Počítačové zpracování obrazu také vrátí logickou hodnotu, která určuje, jestli obrázek je černá a bílá.

## <a name="color-scheme-detection-examples"></a>Barevné schéma detekce příklady

Následující příklad ukazuje odpověď JSON pro počítačové zpracování obrazu vrácený při zjišťování na barevném schématu na obrázku. V tomto případě na obrázku není černá a bílá bitové kopie, ale jsou černá dominantní barvy popředí a pozadí a dominantní barvy pro bitovou kopii jako celek je černobílý.

![Na horách](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Dominantní barva příklady

Následující tabulka popisuje dominantní popředí, pozadí a barvy image pro každý obrázek příkladu vrácená rozhraním pro počítačové zpracování obrazu.

| Image | Dominantní barvy |
|-------|-----------------|
|![Bílé květinu zeleným pozadím](./Images/flower.png)| Popředí: Black<br/>Na pozadí: White<br/>Barvy: Zelená černou, bílou|
![Trénování s prostřednictvím stanice](./Images/train_station.png) | Popředí: Black<br/>Na pozadí: Black<br/>Barvy: Black |

### <a name="accent-color-examples"></a>Příklady Barva motivu

 Následující tabulka popisuje barvu zvýraznění jako hodnotu šestnáctková barva HTML pro každou obrázku vrácená rozhraním pro počítačové zpracování obrazu.

| Image | Doplňková barva |
|-------|--------------|
|![Osoba na rock Horská oblast, abyste se při západu slunce](./Images/mountain_vista.png) | #BB6D10 |
|![Bílé květinu zeleným pozadím](./Images/flower.png) | #C6A205 |
|![Trénování s prostřednictvím stanice](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Černá a bílá detekce příklady

Následující tabulka uvádí, zda je každý obrázek příkladu černé & bílé vrácená rozhraním pro počítačové zpracování obrazu.

| Image | Černá a bílá? |
|-------|----------------|
|![Černá a bílá obrázek budovy v Manhattan](./Images/bw_buildings.png) | true (pravda) |
|![Modré house a front-yard](./Images/house_yard.png) | false (nepravda) |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [zjišťování typy obrázků](concept-detecting-image-types.md).
