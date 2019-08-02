---
title: Postup výuky pomocí Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se s Conversation Learner naučit.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: a18d4c31da4ffeefebd4bda9aa441fdfec062be9
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705266"
---
# <a name="how-to-teach-with-conversation-learner"></a>Výuka s využitím služby Conversation Learner 

Tento dokument vysvětluje, k jakým signálům Conversation Learner ví, a popisuje, jak se učí.  

Výuka se dá rozdělit do dvou různých kroků: extrakce entit a výběr akcí.

## <a name="entity-extraction"></a>Extrakce entit

V rámci pokrývá Conversation Learner používá [Luis](https://www.luis.ai) k extrakci entit.  Pokud jste obeznámeni s LUIS, toto prostředí se vztahuje na extrakci entit v Conversation Learner.

Modely extrakce entit jsou vědomy *obsahu* a *kontextu* v rámci uživatele utterance.  Pokud například slovo "Praha" bylo označeno jako město v jednom utterance, například "Co je počasí v Seattlu", extrakce entit dokáže rozpoznat stejný obsah ("Praha") jako město v jiném utterance, například "populace", i když projevy se velmi liší.  Naopak, pokud je "Francis" rozpoznán jako název v "Naplánování schůzky s Francis", je možné v podobném kontextu rozpoznat nový dříve nezobrazený název, například "nastavit schůzku s dotazem".  Strojové učení je odvozeno z toho, kdy se na základě příkladů školení účastní obsah, kontext nebo obojí.

Extrakce entit v současnosti pouze ví o obsahu aktuální utterance.  Na rozdíl od výběru akcí (níže) neznáte historii dialogů, jako je například předchozí systém, předchozí uživatel nebo dříve rozpoznané entity.  V důsledku toho je chování při extrakci entit "Shared" v rámci všech projevy.  Pokud například uživatel utterance "Chci, aby Apple" měl "Apple" označený jako typ entity "ovoce" v jednom uživatelském utterance, model extrakce entity očekává, že tento utterance ("Chci Applu") má vždy "Apple" označený jako "ovoce".

Pokud se extrakce entit nechová podle očekávání, tady je možné nápravné řešení:

- První věc, kterou můžete vyzkoušet, je přidat další výukové příklady – zejména příklady, které odhalí typický kontext entity (okolní slova), nebo výjimky.
- Pokud je to vhodné, zvažte přidání vlastnosti "Očekávaná entita" do akce.  Další podrobnosti najdete v kurzu o očekávaných entitách.
- I když je možné přidat ruční zpracování `EntityExtractionCallback` k extrakci entit pomocí kódu, jedná se o nejmenší doporučený postup, protože nebude mít výhody vylepšení strojového učení v době, kdy je váš systém vyspělý.

## <a name="action-selection"></a>Výběr akce

Výběr akce používá opakující se neuronové síť, která přijímá jako vstup veškerou historii konverzací.  Výběr akce je tedy stavový proces, který je vědom předchozích projevy uživatelů, hodnot entit a systémových projevy.  

Některé signály jsou přirozeně preferovány procesem učení.  Jinými slovy, pokud Conversation Learner může vysvětlit rozhodnutí o výběru akce pomocí "lépe upřednostňovaných" signálů, bude; Pokud to možné není, bude používat "méně preferované" signály.

Tady je tabulka zobrazující všechny signály v Conversation Learner a ty, které se používají pro výběr akce.  Všimněte si, že pořadí slov v uživatelském projevy je ignorováno.

Nyní | Preference (1 = upřednostňovaná) | Poznámky
--- | --- | --- 
Systémová akce v předchozím tahu | 1 | 
Entity přítomné v aktuální zapínání | 1 | 
Bez ohledu na to, zda se jedná o první zapnutí | 1 |
Přesná shoda slov v aktuální utterance uživatele | 2 | 
Podobná slova v aktuálním uživatelském utterance | 3 | 
Systémové akce před předchozím zapnutím | 4 |
Entity přítomné v zapínání před aktuálním zapnutím | 4 | 
Uživatel projevy před aktuálním zahnutím | 5 | 

> [!NOTE]
> Výběr akce nebere v úvahu obsah systémových akcí – text, obsah karty ani název rozhraní API nebo chování – pouze identitu systémové akce.  V důsledku toho Změna obsahu akce neovlivní chování modelu výběru akce.
>
> Dále platí, že obsah nebo hodnoty entit nejsou použity – pouze jejich přítomnost nebo absence.

Pokud se výběr akce nechová podle očekávání, tady je možné nápravné řešení:

- Přidejte další výuková dialogová okna, zejména dialogová okna, která znázorňují, na které signály se má věnovat výběr akce.  Například pokud výběr akce by měl preferovat jeden signál přes jiný, podělte příklady, které ukazují upřednostňovaný signál ve stejném stavu a ostatní signály proměnlivé.  Některé sekvence můžou několik výukových dialogů, abyste se dozvěděli.
- Přidejte entity Required a diskvalifikovat do definice akcí.  Tato omezení, pokud jsou k dispozici akce, a mohou být užitečná pro vyjádření obchodních pravidel a některých běžných vzorů rozpoznávání. 

## <a name="updates-to-models"></a>Aktualizace modelů

Když v uživatelském rozhraní přidáte nebo upravíte dialogové okno entita, akce nebo výuka, vygeneruje se požadavek na opětovné vytvoření výuky modelu extrakce entit a modelu výběru akce.  Tento požadavek se umístí do fronty a provede se znovu asynchronní školení.  Když je k dispozici nový model, použije se od něj výše pro extrakci entit a výběr akcí.  Tento proces opakovaného školení často trvá přibližně 5 sekund, ale může být delší, pokud je model složitý, nebo pokud je zatížení služby školení vysoké.

Vzhledem k tomu, že školení se provádí asynchronně, je možné, že provedené úpravy nejsou okamžitě odrážet.  Pokud se pro extrakci entit nebo výběr akcí nechovají podle očekávání na základě změn, které jste udělali za posledních 5-10 sekund, může to způsobovat.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Výchozí hodnoty a hranice](./cl-values-and-boundaries.md)
