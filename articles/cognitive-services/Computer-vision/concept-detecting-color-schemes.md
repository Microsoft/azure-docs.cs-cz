---
title: Zjišťování barevná schémata
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Koncepty související se zjišťováním barevné schéma v obrázcích pomocí služeb Azure Cognitive Services pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 66abab93ba9c1152d18428e66d648c6ba690aaa0
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725317"
---
# <a name="detecting-color-schemes"></a>Zjišťování barevná schémata

Počítačové zpracování obrazu extrahuje barvy z obrázku. Barvy se analyzují pak ve třech různých kontextech: popředí dominantní barva, barvu pozadí dominantní a dominantní barvy pro bitovou kopii jako celek. Jsou seskupené do 12 dominantních doplňkových barev. Tyto barvy zvýraznění jsou černá, modrá, brown, šedá, zelená, orange, růžová, nachová, červená, šedozelená, prázdné a žlutou barvou. Počítačové zpracování obrazu analyzuje extrahovat z obrázků se vraťte barvu motivu, který představuje nejvíce živý barvu na obrázku se divákům prostřednictvím kombinace dominantní barvy a sytost barev. V závislosti na barvy v obrázku mohou být vráceny jednoduché černobílý nebo barvy zvýraznění v šestnáctkové kódy. Počítačové zpracování obrazu také vrátí logickou hodnotu, která určuje, jestli obrázek je černá a bílá.

## <a name="color-scheme-detection-examples"></a>Barevné schéma detekce příklady

Následující příklad ukazuje odpověď JSON pro počítačové zpracování obrazu vrácený při zjišťování na barevném schématu na obrázku. V tomto případě na obrázku není černá a bílá bitové kopie, ale jsou černá dominantní barvy popředí a pozadí a dominantní barvy pro bitovou kopii jako celek je černobílý.

![Venkovní Horská oblast](./Images/mountain_vista.png)

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
|![Analýza květinu pro zpracování obrazu](./Images/flower.png)| Popředí: černá<br/>Na pozadí: prázdné<br/>Barvy: Black, White, zelená|
![Pro zpracování obrazu analyzovat trénování stanice](./Images/train_station.png) | Popředí: černá<br/>Na pozadí: černá<br/>Barvy: černá |

### <a name="accent-color-examples"></a>Příklady Barva motivu

 Následující tabulka popisuje barvu zvýraznění jako hodnotu šestnáctková barva HTML pro každou obrázku vrácená rozhraním pro počítačové zpracování obrazu.

| Image | Barva motivu |
|-------|--------------|
|![Venkovní Horská oblast](./Images/mountain_vista.png) | #BB6D10 |
|![Analýza květinu pro zpracování obrazu](./Images/flower.png) | #C6A205 |
|![Pro zpracování obrazu analyzovat trénování stanice](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Černá a bílá detekce příklady

Následující tabulka uvádí, zda je každý obrázek příkladu černé & bílé vrácená rozhraním pro počítačové zpracování obrazu.

| Image | Černá a bílá? |
|-------|----------------|
|![Pro zpracování obrazu analýza sestavení](./Images/bw_buildings.png) | true (pravda) |
|![Pro zpracování obrazu analyzovat House Yard](./Images/house_yard.png) | false (nepravda) |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [zjišťování typy obrázků](concept-detecting-image-types.md).