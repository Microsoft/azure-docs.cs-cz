---
title: Tok řízení konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o toku řízení student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 592ca82db7e0ab3ff89d25b61f38f8b226f3bc86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343215"
---
## <a name="control-flow"></a>Tok řízení

Tento dokument popisuje tok řízení z student konverzace (CL), jak je uvedeno v následujícím obrázku.

![](media/controlflow.PNG)

1. Uživatel zadá termín nebo frázi v robota, například "co je počasí v Praze?"
1. CL předá vstupu uživatele na model strojového učení, který extrahuje entity
    - Tento model je sestavení pomocí student konverzace a hostované www.luis.ai
2. Žádné extrahována entity a uživatelský vstup text, jsou předaný metodě detekce Entity zpětného volání v kódu robota.
    - Tento kód může hodnot sady, zrušte/manipulaci entity
1. CL neuronové sítě pak dojde výstup extrakce entity a vstup uživatele a všechny akce definované v robota skóre
    - V tomto příkladu je poskytnout předpovědi počasí nejvyšší pravděpodobnost akce:

![](media/controlflow_forecast.PNG)

5. Vybraná akce v tomto případě vyžaduje volání rozhraní API pro načtení předpovědi počasí. 
6. Toto rozhraní API, která jsou zapsána pomocí CL. Potom je volána metoda AddAPICallback.  Výsledek toto rozhraní API je poté vrácen uživatele jako zprávu – například "Sunny s vysokou 67."
7. Volání pak přišla neuronové sítě určit další akce podle předchozího kroku.
8. Neuronové sítě pak předpovídá další sadu možných akcí a vybrané akce se zobrazí uživatelům, v takovém případě "Cokoliv jiného?"

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Postup naučit se konverzace student ](./how-to-teach-cl.md)
