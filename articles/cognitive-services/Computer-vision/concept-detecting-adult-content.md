---
title: Popis pro dospělé nebo pikantního obsahu – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související se zjišťováním obsahu pro dospělé nebo pikantního obsahu na obrázcích pomocí rozhraní APi pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312522"
---
# <a name="detect-adult-and-racy-content"></a>Zjištění obsahu pro dospělé nebo pikantního obsahu

Pro počítačové zpracování obrazu dokáže rozpoznat materiál pro dospělé v obrázcích, tak, aby vývojáři můžete omezit zobrazení těchto obrázků v jejich software. Příznaky obsahu se použijí s skóre mezi 0 a 1, tak, aby vývojáři interpretovat výsledky podle vlastních požadavků. 

> [!NOTE]
> Tato funkce také nabízí [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) služby. Zobrazit tuto alternativu k přísnější moderování obsahu scénáře, jako je například moderování textu a pracovních postupů pro recenze prováděné lidmi pro řešení.

## <a name="content-flag-definitions"></a>Příznak obsahu definice

**Pro dospělé** bitové kopie jsou definovány jako ty, které jsou ze své podstaty a často pornografický znázornění nahota a sexuální zneužívání funguje. 

**Pikantní** bitové kopie jsou definovány jako bitové kopie, které jsou ze své podstaty a často sexuálně sugestivní obsahovat méně sexuálně explicitní výrazy obsahu než Image označit jako zařízení **dospělé**. 

## <a name="identify-adult-and-racy-content"></a>Určení obsahu pro dospělé nebo pikantního obsahu

[Analyzovat](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API.

Dvě logické vlastnosti, vrátí metoda analyzovat Image `isAdultContent` a `isRacyContent`, v odpovědi JSON metody, která označuje obsahu pro dospělé nebo pikantního obsahu v uvedeném pořadí. Metoda také vrátí hodnotu dvě vlastnosti `adultScore` a `racyScore`, které představují skóre spolehlivosti pro určení obsahu pro dospělé nebo pikantního obsahu v uvedeném pořadí.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [zjišťování obsahu specifického pro doménu](concept-detecting-domain-content.md) a [zjišťování tváří](concept-detecting-faces.md).
