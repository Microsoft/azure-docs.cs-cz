---
title: Detekce barevného schématu - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Koncepty související s detekcí barevného schématu v obrázcích pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244728"
---
# <a name="detect-color-schemes-in-images"></a>Detekce barevných schémat v obrazech

Počítačové vidění analyzuje barvy v obraze poskytnout tři různé atributy: dominantní barvu popředí, dominantní barvu pozadí a sadu dominantních barev pro obraz jako celek. Vrácené barvy patří do sady: černá, modrá, hnědá, šedá, zelená, oranžová, růžová, fialová, červená, modrozelená, bílá a žlutá. 

Počítačové vidění také extrahuje barvu zvýraznění, která představuje nejživější barvu v obraze, založenou na kombinaci dominantních barev a sytosti. Barva zvýraznění je vrácena jako šestnáctkový barevný kód HTML. 

Počítačové vidění také vrátí logickou hodnotu označující, zda je obraz černobílý.

## <a name="color-scheme-detection-examples"></a>Příklady detekce barevného schématu

Následující příklad ilustruje odpověď JSON vrácenou programem Computer Vision při zjišťování barevného schématu ukázkového obrázku. V tomto případě není ukázkovým obrazem černobílý obraz, ale dominantní barvy popředí a pozadí jsou černé a dominantní barvy pro obraz jako celek jsou černobílé.

![Venkovní hora při západu slunce, s siluetou osoby](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Dominantní barevné příklady

V následující tabulce jsou zobrazeny vrácené barvy popředí, pozadí a obrazu pro každý ukázkový obraz.

| Image | Dominantní barvy |
|-------|-----------------|
|![Bílý květ se zeleným pozadím](./Images/flower.png)| Popředí: Černá<br/>Souvislosti: Bílá<br/>Barvy: Černá, Bílá, Zelená|
![Vlak projíždět stanicí](./Images/train_station.png) | Popředí: Černá<br/>Souvislosti: Černá<br/>Barvy: Černá |

### <a name="accent-color-examples"></a>Příklady barev zvýraznění

 V následující tabulce je zobrazena vrácená barva zvýraznění jako šestnáctková hodnota html pro každý ukázkový obrázek.

| Image | Doplňková barva |
|-------|--------------|
|![Osoba stojící na horské skále při západu slunce](./Images/mountain_vista.png) | #BB6D10 |
|![Bílý květ se zeleným pozadím](./Images/flower.png) | #C6A205 |
|![Vlak projíždět stanicí](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Černé & příklady detekce bílé

V následující tabulce je uvedeno černobílé vyhodnocení počítačového vidění na ukázkových obrázcích.

| Image | Černá & bílá? |
|-------|----------------|
|![Černobílý obraz budov na Manhattanu](./Images/bw_buildings.png) | true |
|![Modrý dům a předzahrádka](./Images/house_yard.png) | false (nepravda) |

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce barevného schématu je součástí rozhraní Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `Color` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"color"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
