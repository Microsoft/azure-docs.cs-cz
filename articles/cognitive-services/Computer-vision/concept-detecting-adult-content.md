---
title: Obsah pro dospělé a pikantní – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s detekcí obsahu pro dospělé a pikantní v obrázcích pomocí rozhraní APi pro Počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ca5f35ab47822d74de556671c38886942d23d9ff
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946288"
---
# <a name="detect-adult-and-racy-content"></a>Zjištění obsahu pro dospělé a pikantní

Počítačové zpracování obrazu dokáže detekovat obsah pro dospělé v obrázcích, aby vývojáři mohli omezit zobrazení takových imagí v softwaru. Příznaky obsahu jsou aplikovány se skóre mezi nulou a jedním, aby vývojáři mohli interpretovat výsledky podle jejich vlastních předvoleb. 

> [!NOTE]
> Tuto funkci nabízí i služba [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) . V této Alternative najdete řešení pro přísnější scénáře Moderování obsahu, jako je například moderování textu a pracovní postupy pro lidské kontroly.

## <a name="content-flag-definitions"></a>Definice příznaků obsahu

Obrázky **pro dospělé** se definují jako ty, které jsou pornografický v podstatě, a často vymezují nahotu a pohlavní úkony. 

**Pikantní** obrázky jsou definovány jako obrázky, které jsou pohlavní sugestivní v podstatě, a často obsahují méně zřejmý sexuální obsah než obrázky označené jako **dospělý**. 

## <a name="identify-adult-and-racy-content"></a>Identifikace obsahu pro dospělé a pikantní

Rozhraní API pro [analýzu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

Metoda analyzovat obrázek vrátí dvě logické vlastnosti `isAdultContent` a `isRacyContent`v rámci odpovědi JSON metody k označení obsahu pro dospělého a pikantního v uvedeném pořadí. Metoda také vrátí dvě vlastnosti, `adultScore` `racyScore`které reprezentují hodnocení spolehlivosti pro identifikaci dospělého a pikantní obsahu.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [zjišťování obsahu specifického pro doménu](concept-detecting-domain-content.md) a [detekce ploch](concept-detecting-faces.md).
