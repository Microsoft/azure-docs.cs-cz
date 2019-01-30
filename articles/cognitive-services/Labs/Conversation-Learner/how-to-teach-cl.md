---
title: Jak se naučit s konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak se naučit s Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220259"
---
# <a name="how-to-teach-with-conversation-learner"></a>Výuka s využitím služby Conversation Learner 

Tento dokument popisuje, co signalizuje konverzace Learner si je vědoma a popisuje, jak se naučí.  

Výuka lze rozložit na dva samostatné kroky: extrakce entity a výběr akce.

## <a name="entity-extraction"></a>Extrakce entity

Pod pokličkou, konverzace student používá [LUIS](https://www.luis.ai) pro extrakci entity.  Pokud jste se seznámili s LUIS, prostředí vztahující se k extrakci entity v Learner konverzace.

Víme modely extrakce entity *obsah* a *kontextu* v rámci utterance uživatele.  Pokud slovo "Seattle" byly označeny jako Město v jedné utterance, jako například "Jaký je o počasí v Praze", extrakce entity je schopen uznání stejný obsah ("Seattle") jako Město v jiném utterance, jako je například "Naplnění Seattle", i v případě, projevy se velmi liší.  Naopak pokud má "Francis" uznávaná jako název v "Naplánovat schůzku s Francis", pak nový název dříve nezobrazený lze rozpoznat v kontextu podobné, jako je "Sada schůzku s Robin".  Machine learning odvodí, kdy k účasti na obsah, místní nebo obojí, podle příkladů, školení.

V současné době se pouze extrakce entity vědět obsah aktuální utterance.  Na rozdíl od výběr akce (dole) není vědět, dialogové okno historie například předchozí systému zapne, předchozí uživatel zapne nebo dříve rozpoznaný entity.  V důsledku toho chování extrakce entity je "sdílený" všechny projevy.  Například pokud uživatel utterance "Chci, aby Apple" "Apple", které jsou označeny jako "Ovoce" typ entity v jedné utterance uživatele, extrakce entity model bude očekávat, že tento utterance ("Chci, aby Apple") by měl mít vždy "Apple", které jsou označeny jako "Ovoce".

Pokud se extrakce entity se nechová podle očekávání, tady jsou mezi možné nápravy:

- První věc, kterou akci má přidat další příklady školení – zejména příklady, které zjistí kontext typické entity (okolní slov) nebo výjimky
- V případě potřeby, zvažte možnost přidat akci, vlastnost "Očekáván entit".  Další podrobnosti najdete v kurzu u entit očekávání.
- I když je možné přidat ruční zpracování `EntityExtractionCallback` extrahovat entity pomocí kódu, totiž nejméně doporučený postup nebude těžit z vylepšení v machine learning zrání vašeho systému.

## <a name="action-selection"></a>Výběr akce

Výběr akce používá běžné neuronové sítě, která přijímá jako vstupní všechny konverzace historie.  Výběr akce tedy stavových procesů, který zná předchozí projevy uživatele, hodnoty entit a projevy systému.  

Některé signály se přirozeně upřednostňuje studijní postup.  Jinými slovy Pokud Learner konverzace se vysvětlují rozhodnutí o akce výběru pomocí více "upřednostňované" signály, bude; v případě nedostupnosti se bude používat méně "upřednostňované" signálů.

Následuje tabulka zobrazující všechny signály Learner konverzace a ty, které jsou používány výběr akce.  Všimněte si, že slovo pořadí v projevy uživatele se ignoruje.

Signál | Předvolby (1 = upřednostňovaným) | Poznámky
--- | --- | --- 
Akce systému v důsledku předchozí | 1 | 
Entity k dispozici v důsledku aktuální | 1 | 
Zda se jedná o první zapnout | 1 |
Přesná shoda slova v aktuální utterance uživatele | 2 | 
Podobně jako význam slova v aktuální utterance uživatele | 3 | 
Zapnout akcí v systému před předchozí | 4 |
Entity k dispozici na oplátku před aktuální zapnout | 4 | 
Zapnout projevy uživatele před aktuální | 5 | 

> [!NOTE]
> Výběr akce nevyužívá obsah akcí v systému – text, obsah karty, nebo název rozhraní API nebo chování – pouze identity akce systému.  V důsledku toho při změně obsahu akci nemění chování výběru modelu akce.
>
> Dále obsah a hodnoty entit nejsou použít – pouze jejich přítomnosti nebo absenci.

Pokud výběr akce se nechová podle očekávání, tady jsou možné náhrad:

- Přidejte další dialogová okna trénování, zejména dialogová okna, které ilustrují signály, které výběr akce by měla být dejte pozor na.  Například pokud výběr akce by měla přednost jednoho signálu před jiným, uvádí příklady aplikace, které ukazují upřednostňované signálu se v takovém stavu a jinými signály různé.  Některé sekvence může trvat několik dialogů školení Další.
- Přidáte "Povinné" a "Vyřazení" entity, které definice akce.  Tato omezení při akce jsou k dispozici a může být užitečné pro rychlé obchodní pravidla a některé vzory zdravý. 

## <a name="updates-to-models"></a>Aktualizace modelů

Kdykoli přidat nebo upravit entity, akce nebo trénování dialogového okna v uživatelském rozhraní, tím se vytvoří požadavek na opětovné trénování modelu extrakce entity a výběr modelu akce.  Tento požadavek je umístěn ve frontě a znovu školení se provádí asynchronně.  Pokud je k dispozici nový model, používá se od tohoto okamžiku a vyšší při výběru extrakce a akce entity.  Tento proces znovu trénovací často trvá přibližně 5 sekund, ale může být déle, pokud model je komplexní, nebo pokud je vysoké zatížení služby školení.

Protože školení se provádí asynchronně, je možné, že se okamžitě neprojeví úpravy, které jste udělali.  Pokud se extrakce nebo akce výběru entity se nechová podle očekávání, na základě změn, které jste udělali v posledních 5 až 10 sekund, to může být příčinou.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Výchozí hodnoty a hranice](./cl-values-and-boundaries.md)
