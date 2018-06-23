---
title: Postup naučit se konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se naučit se student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 639fea64fc8eeb2c1f6e6240c4eb26efc68febbd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343227"
---
# <a name="how-to-teach-with-conversation-learner"></a>Postup naučit se konverzace student 

Tento dokument popisuje, co dá signál konverzace student si je vědoma a popisuje, jak zjišťuje.  

Vyučující lze rozložit na dvě odlišné kroky: extrakce entity a výběr akce.

## <a name="entity-extraction"></a>Extrakce entity

V pozadí, používá konverzace Student [LEOŠ](https://www.luis.ai) pro extrakci entity.  Pokud jste obeznámeni s LEOŠ, prostředí vztahující se k extrakci entity v student konverzace.

Modely extrakce entity si vědomi *obsah* a *kontextu* v rámci utterance uživatele.  Pokud slovo "Seattle" byla označena jako Město v jedné utterance, jako například "Jaký je počasí v Praze", extrakce entity je schopen rozpozná stejný obsah ("Seattle") jako Město v jiné utterance, jako je například "Naplnění Seattle", i když utterances se příliš neliší.  Naopak pokud má "Francis" byla rozpoznána jako název v "Plánu schůzku s Francis", pak nový název dříve neviditelný může rozpoznat v kontextu podobné, jako je "Nastavení schůzku s každý s každým".  Odvodí strojové učení, kdy k účasti na obsah, kontextu nebo obojí, podle příklady školení.

V současné době je jenom entity extrakce vědět obsah aktuální utterance.  Na rozdíl od výběr akce (dole) totiž neví o dialogu historie například předchozí oplátku systému, předchozí uživatel zapne nebo dříve rozpoznaný entity.  V důsledku toho chování entit extrakce je "sdílené" mezi všechny utterances.  Například pokud utterance uživatele "Chci, aby Apple" "Apple", které jsou označené jako typ entity "Výsledek" v utterance jeden uživatel, model entity extrakce se očekávají, že tento utterance ("Chci, aby Apple") by měl mít vždy "Apple", které jsou označené jako "Výsledek".

Pokud extrakce entity není chovají podle očekávání, zde jsou možná náhrad:

- První věc pokusit je přidat další příklady školení – zvlášť příklady, které se zobrazí v kontextu typické entity (které obaluje slova) nebo výjimky
- Je vhodné přidat vlastnost "Očekává Entity" akce, podle potřeby.  Další podrobnosti najdete v kurzu na očekávaný entity.
- I když je možné přidat ruční zpracování `EntityExtractionCallback` k extrakci entity pomocí kódu, totiž minimální doporučený postup nebude využívat vylepšení v nástroji machine learning během existence systému.

## <a name="action-selection"></a>Výběr akce

Výběr akce používá opakující neuronové sítě, který přebírá jako vstupní všechny historii konverzace.  Proto je výběr akce stavová proces, který zná předchozí utterances uživatele, hodnot entity a utterances systému.  

Některé signály se přirozeně upřednostňovaná procesem učení.  Jinými slovy Pokud konverzace student mohou vysvětlovat výběr rozhodnutí o akce pomocí více "upřednostňované" signály, ji budou; Pokud ne, pak použije méně "upřednostňované" signály.

Následuje tabulka zobrazující všechny signály v student konverzace a ty, které jsou používány výběr akce.  Všimněte si, že aplikace word pořadí v utterances uživatele se ignoruje.

Signál | Předvolby (1 = nejvíc preferovaný) | Poznámky
--- | --- | --- 
V předchozích zapnout akci systému | 1 | 
Entity k dispozici v aktuální zapnout | 1 | 
Jestli se jedná o první zapnout | 1 |
Přesná shoda slova v aktuální utterance uživatele | 2 | 
Podobá význam slova v aktuální utterance uživatele | 3 | 
Akce systému před předchozí zapnout | 4 |
Entity k dispozici na před aktuální vypněte oplátku | 4 | 
Zapnout utterances uživatele před aktuální | 5 | 

Všimněte si, že výběr akce trvat obsah systému akce – text, karta obsah, nebo název rozhraní API nebo chování – jenom identitě systému akce.  Změna obsahu akce v důsledku toho nebude změnit chování modelu výběr akce.

Další Všimněte si, že obsah nebo hodnoty entit nejsou použít – pouze jejich přítomnosti nebo absenci.

Pokud výběr akce není chovají podle očekávání, zde jsou možná náhrad:

- Přidejte další dialogová okna train, zejména dialogová okna, které ilustrují signály, které výběr akce by měla být důrazem.  Například pokud výběr akce by měla přednost jeden signál oproti jinému, dejte příklady, které ukazují upřednostňované signál se v takovém stavu a jinými signály různých.  Některé sekvence může trvat několik dialogů školení Další.
- Přidejte "Povinné" a "Vyřazení" entity k definice akce.  Toto omezení při akce jsou k dispozici a může být užitečná pro expresní obchodní pravidla a některé běžné smysl vzory. 

## <a name="updates-to-models"></a>Aktualizace na modely

Kdykoli přidat nebo upravit entitu, akce nebo dialogové okno train v uživatelském rozhraní, tak se vytvoří žádost o znovu trénování modelu entity extrakce i modelu výběr akce.  Tento požadavek je umístěn ve frontě a znovu cvičení probíhá asynchronně.  Když nový model je k dispozici, použije se z tohoto bodu dále pro výběr extrakce a akce entity.  Tento proces znovu školení často trvá přibližně 5 sekund, ale může být delší, pokud je model komplexní, nebo pokud zatížení službu školení je vysoká.

Protože školení probíhá asynchronně, je možné, že úpravy, které jste udělali, neprojeví se okamžitě.  Pokud výběr extrakce nebo akce entity není chovají podle očekávání, na základě změn, které jste udělali v posledních 5-10 sekund, to může být příčinou.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Výchozí hodnoty a hranice](./cl-values-and-boundaries.md)
