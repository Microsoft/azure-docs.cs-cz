---
title: Co je Conversation Learner? -Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Přečtěte si o Conversation Learner a o tom, jak funguje.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 725a1d3628fb320a58e073fe2d825af23b02c0bd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707018"
---
# <a name="what-is-conversation-learner"></a>Co je Conversation Learner?

Conversation Learner vám umožní sestavovat a naučit konverzační rozhraní, která se naučíte z ukázkových interakcí. 

Na rozdíl od tradičních přístupů Conversation Learner považuje koncový kontext dialogu za účelem vylepšení reakcí a poskytování působivější uživatelské prostředí. V rámci nejrůznějších způsobů použití orientovaných na úlohy, Conversation Learner na pozadí aplikuje strojové učení, aby roboty a inteligentní agenti byli méně pravděpodobnější, že frustrovat uživatelé, účtují další poplatky za služby zákazníkům a spurější interakce.

Vývojáři začnou zadávat typický dialogy, které chtějí napodobovat. Model se učí, jak jsou zadávány další dialogová okna. Jakmile model pracuje správně, může se robot nasazovat koncovým uživatelům. Conversation Learner zaznamená konverzace s uživateli a vývojář je může zkontrolovat. Pokud jsou chyby Spotted, vývojář může provést opravu na místě a model se přeškolí a k dispozici pro okamžité použití.

Tento přístup omezuje ruční kódování řídicích postupů dialogu a umožňuje vlastníkům a odborníkům v doméně přispívat do konverzačního rozhraní bez předchozího vědomí ve strojovém učení. Bez ohledu na to, jestli je nasazený jako součást robota, inteligentního zařízení nebo inteligentního agenta, Conversation Learner může rychle iterovat nové dovednosti, chování nebo kompetence a rychle vylepšit jejich kvalitu. 

Conversation Learner vývojářům umožňuje zvýšit množství možností na uvedení na trh a řídit úspěšná dialogová okna napříč různými kanály v rámci několika konverzací prostřednictvím rozhraní Microsoft bot Framework nebo samostatně pomocí vlastní infrastruktury.

Shrnutí a zvýraznění:

- Conversation Learner je prvním způsobem, jak sestavovat roboty orientované na úlohy.

- Spoléhá se na ucelenou reneuronové síť (LSTM), která se naučí přímo z několika příkladů konverzací. 

- Umožňuje návrhářům, vývojářům, obchodním uživatelům a pracovníkům centra volání vytvářet a spravovat roboty. 

- Poskytuje možnost vyjádřit obchodní pravidla a běžné smysly v kódu.

- V rámci výukových cvičení se model sítě neuronové používá k určení skóre další sady očekávaných akcí v konverzaci. Vývojář robota pak může vybrat správnou akci a vyškolit síť, aby poskytovala správnou odpověď.
 
- Po dokončení školení může vývojář pomocí dialogových oken protokolu z interakcí s uživateli provádět opravy odpovědí robota a předávat model. 

- Může volat rozhraní API specifická pro doménu a třetí strany k dokončení úkolů.

