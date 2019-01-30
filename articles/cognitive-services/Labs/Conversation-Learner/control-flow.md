---
title: Tok řízení konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o toku řízení Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07f4506b7dd0ac8ca0462e2a418983e561859c91
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208376"
---
## <a name="control-flow"></a>Tok řízení

Tento dokument popisuje tok řízení z Learner konverzace (CL) se zobrazuje následujícím obrázku.

![](media/controlflow.PNG)

1. Uživatel zadá termín nebo frázi v robota, například "co je o počasí v Praze?"
1. CL předá uživatelský vstup do modelu strojového učení, který extrahuje entity
    - Tento model je sestavení learner konverzace a hostitelem www.luis.ai
1. Extrahovány žádné entity, a zadání textu, jsou předány metodě zpětného volání zjišťování entit v bodu robotů také kódu.
    - Tento kód může hodnoty set/clear/manipulaci s entit
1. CL neuronové sítě převezme výstup extrakce entity a uživatelský vstup a skóre, které se všechny akce definované v robota
    - V tomto příkladu je poskytnout předpověď počasí nejvyšší pravděpodobnost akce:

    ![](media/controlflow_forecast.PNG)

1. Vybraná akce v tomto případě vyžaduje volání rozhraní API k načtení předpověď počasí. 
1. Toto rozhraní API, která měla byla registrována pomocí CL. Potom je volána metoda AddCallback.  Výsledek tohoto rozhraní API se vrátili, aby uživatel jako zpráva – například "Sunny s vysokou 67."
1. Volání je pak provedeno neuronové sítě určit další akce podle předchozího kroku.
1. Neuronové sítě pak předpovídá další sadu možných akcí a vybrané akce se budou zobrazovat uživateli, v tomto případě "Cokoli?"

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Tom, jak se naučit s Learner konverzace ](./how-to-teach-cl.md)
