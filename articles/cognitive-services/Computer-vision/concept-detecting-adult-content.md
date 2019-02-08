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
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 64db05e5e40b76d219ea0e3214c20297f32da4b5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861268"
---
# <a name="detecting-adult-and-racy-content"></a>Rozpoznávání obsahu pro dospělé a nevhodného obsahu

Mezi různými obrazovými kategoriemi je i skupina nevhodného obsahu a obsahu pro dospělé. Tato skupina umožňuje zjišťovat materiály pro dospělé a omezuje zobrazování obrázků se sexuálním obsahem. Filtr pro rozpoznání nevhodného obsahu a obsahu pro dospělé je možné podle potřeby nastavit na posuvné stupnici podle preferencí uživatele.

## <a name="defining-adult-and-racy-content"></a>Definování obsahu pro dospělé nebo pikantního obsahu

Mezi různé funkce visual vztahuje [analyzovat Image metoda](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), dospělé vizuální funkce umožňuje detekovat obrázků pro dospělé nebo pikantního. "Pro dospělé" image jsou definovány jako ty, které jsou ze své podstaty pornografický a často znázornění nahota a sexuální zneužívání funguje. "Pikantní" imagí, které jsou definovány jako bitové kopie, které jsou ze své podstaty sexuálně sugestivní a často obsahují méně sexuálně explicitní výrazy obsahu než imagí, které jsou označené jako "Obsah pro dospělé." Typ pro dospělé vizuální funkce se běžně používá k omezení zobrazení Image obsahující sexuálně sugestivní a explicitně sexuální zneužívání obsah.

## <a name="identifying-adult-and-racy-content"></a>Určení obsahu pro dospělé nebo pikantního obsahu

Dvě vlastnosti, vrátí metoda analyzovat Image `isAdultContent` a `isRacyContent`, v odpovědi JSON metody, která označuje, respektive obsahu pro dospělé nebo pikantního obsahu. Obě vlastnosti vrací logickou hodnotu true nebo false. Metoda také vrátí hodnotu dvě vlastnosti `adultScore` a `racyScore`, které představují, v uvedeném pořadí, hodnocení spolehlivosti pro určení obsahu pro dospělé nebo pikantního obsahu. Filtr spolehlivosti pro dospělé nebo pikantního obsahu zjišťování můžete nastavit na posuvné stupnice tak, aby vyhovovaly dle požadavků na základě konkrétního scénáře.

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [zjišťování obsahu specifického pro doménu](concept-detecting-domain-content.md) a [zjišťování tváří](concept-detecting-faces.md).
