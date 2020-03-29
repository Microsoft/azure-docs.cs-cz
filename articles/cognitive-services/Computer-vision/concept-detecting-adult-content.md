---
title: Dospělý, pikantní, krvavý obsah - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se detekce obsahu pro dospělé v obrázcích pomocí počítače Vision APi.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718518"
---
# <a name="detect-adult-content"></a>Detekce obsahu pro dospělé

Počítačové vidění dokáže detekovat materiál pro dospělé na obrázcích, takže vývojáři mohou omezit zobrazování těchto obrázků ve svém softwaru. Příznaky obsahu jsou použity se skóre mezi nulou a jedním, takže vývojáři mohou interpretovat výsledky podle svých vlastních preferencí.

> [!NOTE]
> Většinu těchto funkcí nabízí služba [Azure Content Moderator.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Tato alternativa najdete v této alternativě, kde najdete řešení přísnějších scénářů moderování obsahu, jako je moderování textu a pracovní postupy lidské kontroly.

## <a name="content-flag-definitions"></a>Definice příznaku obsahu

V rámci klasifikace "dospělých" je několik různých kategorií:

- **Obrázky pro dospělé** jsou definovány jako ty, které jsou výslovně sexuální povahy a často zobrazují nahotu a sexuální akty.
- Pikantní obrázky jsou definovány jako **obrázky,** které jsou sexuálně sugestivní povahy a často obsahují méně sexuálně explicitní obsah než obrázky označené jako **dospělý**.
- Krvavé obrazy jsou **definovány** jako ty, které zobrazují gore.

## <a name="use-the-api"></a>Použití rozhraní API

Obsah pro dospělé můžete zjistit pomocí [rozhraní Analyzovat rozhraní API pro obrázky.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Když přidáte hodnotu `Adult` parametru dotazu **visualFeatures,** rozhraní API&mdash;`isAdultContent` `isRacyContent`vrátí `isGoryContent` &mdash;tři logické vlastnosti a v odpovědi JSON. Metoda také vrátí&mdash;`adultScore`odpovídající `racyScore`vlastnosti , a `goreScore` &mdash;které představují skóre spolehlivosti mezi nulou a jednou pro každou příslušnou kategorii.

- [Úvodní příručka: Analýza bitové kopie (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
