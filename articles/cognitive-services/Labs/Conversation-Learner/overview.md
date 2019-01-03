---
title: Co je Conversation Learner? – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o konverzaci Learner a jak to funguje.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 0d2cca335206ee59b922a31ed3cf3ba24ef4ecd7
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790771"
---
# <a name="what-is-conversation-learner"></a>Co je Conversation Learner?

Konverzace Learner umožňuje vytvářet a naučit konverzační rozhraní, které se učí z příkladu interakce. 

Na rozdíl od tradiční přístupy konverzace Learner bere v úvahu kontext začátku do konce dialogu za účelem zlepšení odezvy a zajištění více poutavé prostředí uživatele. Pokrývání uzlů širokou škálu orientovaných případy použití, konverzace Learner platí strojového učení na pozadí, aby roboty a inteligentní agentů méně pravděpodobné, že frustrovat uživatele, vynakládá dalších zákaznických služeb a více podporují více intuitivní interakce.

Vývojáři, začněte zadáním typický dialogová okna, které chtějí napodobují. Model se učí se zadal další dialogová okna. Jakmile Model funguje dobře, robota může nasadit koncovým uživatelům. Konverzace Learner protokoly konverzace s uživateli a vývojáři můžou Seznamte se s nimi. Pokud jsou nanese chyby, vývojář může opravu na místě, a je model retrained a dostupné k použití okamžitě.

Tento přístup snižuje ruční kódování logiky ovládacího prvku dialogu a umožňuje vlastníkům obchodních nebo odborníky na domény přispívat k konverzační rozhraní bez předchozího strojového učení znalostní báze. Zda je nasazen jako součást robota, inteligentní zařízení nebo inteligentní agenta, konverzace Learner můžete rychle iterovat nové dovednosti, chování nebo činnost a rychle zlepšit jejich kvalitu. 

Konverzace Learner umožňuje vývojářům zvýšit rychlost uvedení na trh a jednotky úspěšné dialogy napříč několika kanály konverzační prostřednictvím Microsoft Bot Framework, nebo samostatně pomocí vlastní infrastruktury.

Shrnutí a vybraná vystoupení:

- Konverzace Learner je to první AI způsob vytváření robotů orientovaných na úlohy.

- Využívá začátku do konce opakujícím neuronové sítě (LSTM) a učí přímo z více zapnout příklady konverzace. 

- Umožňuje návrháři, vývojáře, podnikoví uživatelé a pracovníky centra volání sestavovat a spravovat roboty. 

- Poskytuje možnost express obchodní pravidla a zdravý rozum v kódu.

- Během výuky relací, modelu neuronové sítě slouží ke stanovení skóre pro další sadu očekávané akce v konverzaci. Vývojáři robotů můžete vybrat správnou akci a trénování sítě, zajistit správné odpovědi.
 
- Po dokončení školení vývojáři pomocí protokolu dialogů z interakce uživatele k provedení opravy odpovědi robotů a přeučování modelu. 

- Můžete volat rozhraní API specifického pro doménu a třetích stran k dokončení úloh.

