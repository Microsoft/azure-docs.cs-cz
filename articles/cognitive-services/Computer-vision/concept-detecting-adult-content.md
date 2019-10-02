---
title: Dospělý, pikantní, gorie obsahu – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se rozpoznávání obsahu pro dospělé v obrázcích pomocí rozhraní Počítačové zpracování obrazu APi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718518"
---
# <a name="detect-adult-content"></a>Zjistit obsah pro dospělé

Počítačové zpracování obrazu dokáže detekovat obsah pro dospělé v obrázcích, aby vývojáři mohli omezit zobrazení těchto imagí v softwaru. Příznaky obsahu jsou aplikovány se skóre mezi nulou a jedním, aby vývojáři mohli interpretovat výsledky podle jejich vlastních předvoleb.

> [!NOTE]
> Mnohé z těchto funkcí nabízí služba [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) . V této Alternative najdete řešení pro přísnější scénáře Moderování obsahu, jako je například moderování textu a pracovní postupy pro lidské kontroly.

## <a name="content-flag-definitions"></a>Definice příznaků obsahu

V rámci klasifikace "dospělý" je několik různých kategorií:

- Obrázky **pro dospělé** se definují jako ty, které jsou výslovně pohlavní v podstatě a často vycházejí z nahoty a pohlavního chování.
- **Pikantní** obrázky jsou definovány jako obrázky, které jsou pohlavní sugestivní v podstatě, a často obsahují méně zřejmý sexuální obsah než obrázky označené jako **dospělý**.
- **Gorie** obrázky jsou definovány jako ty, které vizuálně Gore.

## <a name="use-the-api"></a>Použití rozhraní API

Obsah pro dospělé můžete detekovat pomocí rozhraní API pro [analýzu imagí](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Když do parametru dotazu **visualFeatures** přidáte hodnotu `Adult`, vrátí rozhraní API tři logické vlastnosti @ no__t-2 @ no__t-3, `isRacyContent` a `isGoryContent` @ no__t-6in jeho odpověď JSON. Metoda také vrátí odpovídající vlastnosti @ no__t-0 @ no__t-1, `racyScore` a `goreScore` @ no__t-4which představuje hodnocení spolehlivosti mezi nulou a jednou pro každou příslušnou kategorii.

- [Rychlý Start: analýza obrázku (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Rychlý Start: analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
