---
title: Zjišťování barevná schémata - pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související se zjišťováním barevné schéma v obrázků s využitím rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6b25da9b2569b0185d41684c45a22a3eb3377511
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313071"
---
# <a name="detect-color-schemes-in-images"></a>Zjištění barevná schémata v obrázcích

Počítačové zpracování obrazu analyzuje barvy obrázku poskytuje tři různé atributy: barvu popředí dominantní barva pozadí dominantní a sadu dominantní barvy pro bitovou kopii jako celek. Vrátí barvy patří do sady: černou, modrá, hnědá, šedá, zelená, orange, růžová, nachová, red, šedozelená, prázdné a žlutou barvou. 

Počítačové zpracování obrazu extrahuje také barvu motivu, který představuje největší živý barvu na obrázku, založené na kombinaci dominantní barvy a sytosti. Barva motivu se vrátí jako šestnáctkový kód barvy HTML. 

Počítačové zpracování obrazu také vrátí hodnotu typu boolean označující, jestli obrázek je černobílý.

## <a name="color-scheme-detection-examples"></a>Barevné schéma detekce příklady

Následující příklad ukazuje odpověď JSON pro počítačové zpracování obrazu vrácený při zjišťování na barevném schématu na obrázku. V tomto případě na obrázku není černobílý bitové kopie, ale jsou černá dominantní popředí a pozadí a dominantní barvy pro bitovou kopii jako celek je černobílý.

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

V následující tabulce jsou uvedeny vrácené popředí, pozadí a barvy image pro každý Ukázkový obrázek.

| Image | Dominantní barvy |
|-------|-----------------|
|![Bílé květinu zeleným pozadím](./Images/flower.png)| Popředí: Black<br/>Na pozadí: White<br/>Barvy: Zelená černou, bílou|
![Trénování s prostřednictvím stanice](./Images/train_station.png) | Popředí: Black<br/>Na pozadí: Black<br/>Barvy: Black |

### <a name="accent-color-examples"></a>Příklady Barva motivu

 Vrácené barva jako šestnáctkovou hodnotu barvy HTML, pro každý obrázek příkladu v následující tabulce.

| Image | Doplňková barva |
|-------|--------------|
|![Osoba na rock Horská oblast, abyste se při západu slunce](./Images/mountain_vista.png) | #BB6D10 |
|![Bílé květinu zeleným pozadím](./Images/flower.png) | #C6A205 |
|![Trénování s prostřednictvím stanice](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Černá a bílá detekce příklady

Následující tabulka uvádí černobílé vyhodnocení počítačové zpracování obrazu v ukázkové obrázky.

| Image | Černá a bílá? |
|-------|----------------|
|![Černá a bílá obrázek budovy v Manhattan](./Images/bw_buildings.png) | true (pravda) |
|![Modré house a front-yard](./Images/house_yard.png) | false (nepravda) |

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [zjišťování typy obrázků](concept-detecting-image-types.md).
