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
ms.openlocfilehash: 7e41eb0f6a61f7b195e251739ae93207c731cac5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535873"
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

Obsah pro dospělé můžete detekovat pomocí rozhraní API pro [analýzu imagí](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Když přidáte hodnotu `Adult` do parametru dotazu **visualFeatures** , vrátí rozhraní API tři logické vlastnosti &mdash; `isAdultContent` , `isRacyContent` a `isGoryContent` &mdash; v jeho odpovědi JSON. Metoda také vrátí odpovídající vlastnosti &mdash; `adultScore` , `racyScore` a, `goreScore` &mdash; které reprezentují výsledky spolehlivosti mezi nulou a jednou pro každou příslušnou kategorii.

- [Rychlý Start: analýza obrázku (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Rychlý Start: analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
