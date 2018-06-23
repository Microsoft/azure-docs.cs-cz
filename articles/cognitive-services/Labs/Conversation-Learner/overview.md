---
title: Co je konverzace student? -Microsoft kognitivní služby | Microsoft Docs
titleSuffix: Azure
description: Další informace o student konverzace a jak to funguje.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9cbf0e60382ef17d68aab47cf5f24ea9b8434f13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343234"
---
# <a name="what-is-conversation-learner"></a>Co je konverzace student?

Konverzace student umožňuje vytvářet a naučit konverzačního rozhraní, které se učí z příkladu interakce. 

Na rozdíl od tradičních přístupy zvažuje konverzace student začátku do konce kontextu dialogu za účelem zlepšení odpovědí a poskytovat více poutavé koncových uživatelů. Pokrývání uzlů širokou škálu orientované na úlohy případy použití, konverzace student platí počítač zkušenosti na pozadí, aby robotů a inteligentní agenty méně pravděpodobné, že frustrovat uživatele, způsobit další zákazník služby náklady a intuitivnější pro interakci s.

Abyste mohli začít, zadá vývojář Typickým případem dialogová okna, které chtějí napodobují. Jako další dialogová okna jsou vloženy, model se průběžně aktualizuje a vývojáře, můžete zjistit, jak dobře generalizací modelu. Jakmile model funguje dobře, lze nasadit robota koncovým uživatelům. Konverzace student zaprotokoluje konverzace s uživateli a vývojář zkontrolovat. Pokud se nanese chyb, vývojáře můžete provést opravu na místě a je model retrained a dostupné k použití okamžitě.

Tento přístup snižuje ruční kódování dialogu řízení logiky a umožňuje vlastníkům obchodních nebo domény odborníky přispívat k konverzačního rozhraní bez předchozího strojového učení znalostní báze. Jestli je nasazená jako součást robota, inteligentní zařízení nebo inteligentní agent, student konverzace můžete rychle iterovat nové dovednosti, chování nebo možnosti a rychle zlepšení jejich kvality. 

Konverzace student umožňuje vývojářům zvýšit rychlost na trh a jednotky úspěšné dialogy napříč více konverzačního kanály prostřednictvím Microsoft robota Framework, nebo samostatně pomocí vlastní infrastrukturu.

Souhrn a označuje:

- Konverzace student je AI první způsob vytváření robotů orientované na úlohy.

- Používá opakující neuronové síť začátku do konce (LSTM) který zjišťuje přímo z několika zapnout příklady konverzace. 

- Umožňuje návrhářů, vývojáři, podnikoví uživatelé a pracovníci center volání tvorbu a udržování robotů. 

- Poskytuje možnost express obchodní pravidla a běžné smysl v kódu.

- Během výuky relací za modelu neuronové sítě slouží ke stanovení skóre další sadu očekávané akcí v konverzaci. Vývojář robota můžete pak vybrat správnou akci a cvičení sítě zajistit správné odpovědi.
 
- Po dokončení školení vývojář pomocí protokolu dialogů z interakce uživatele k provedení oprav k odpovědím robota a přeučování modelu. 

- Můžete volat rozhraní API specifické pro doménu a třetích stran k dokončení úlohy.

