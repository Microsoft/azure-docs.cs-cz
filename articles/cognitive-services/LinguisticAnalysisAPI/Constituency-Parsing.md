---
title: Volebním analýza v lingvistické Analysis API | Microsoft Docs
description: Informace o tom, jak volebním analýza, také známé jako "fráze analýzy struktura" identifikuje frází v textu.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: lesun
ms.openlocfilehash: bff5e587621e1278c260d555aec280a0f4c7c8a1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082168"
---
# <a name="constituency-parsing"></a>Volebním analýza

Cílem volebním analýza (také označované jako "struktura fráze analýza") je k identifikaci frází v textu.
To může být užitečné při extrahování informací z textu.
Zákazníci může být vhodné k vyhledání názvů funkcí nebo klíče frází z velkých textu textu a zobrazíte modifikátory a akce, které obaluje každé takové frázi.

## <a name="phrases"></a>Fráze

K linguist *frázi* je více než jen pořadí slova.
Skupina slov být fráze, musí k přehrání určité role ve větě použít současně.
Této skupiny slova můžete přesunout společně nebo nahrazena jako celek, a věty by měla zůstat fluent a gramatické.

Vezměte v úvahu příkaze

> Chci najít nové automobilu hybridní s Bluetooth.

Fráze podstatné jméno obsahuje tuto větu: "nové hybridní automobilu s Bluetooth".
Jak jsme si vědomi, že se jedná o fráze?
Jsme (poněkud poetically) revize se věta přesunutím této celou frázi před:

> Nové hybridní automobilu s Bluetooth, které chcete najít.

Nebo frázi podobné funkce a význam, například "zvláštní nové auto" jsme může nahradit tuto frázi:

> Chci najít zvláštní nové auto.

Pokud místo toho jsme zachyceny jinou podmnožinou slova, tyto úlohy nahrazení by vedlo k neobvyklé či nečitelných věty.
Zvažte, co se stane, když jsme přesunout "najít novou" na stránce do popředí:

> Zjistí, který chcete hybridní automobilu s Bluetooth nový.

Výsledky je velmi obtížné číst a pochopit.

Cílem Analyzátor je najít všechny takové věty.
Interestingly v přirozeném jazyce, frází zpravidla vnořit do sebe navzájem.
Fyzická reprezentace těchto frází je strom, například následující:

![Strom](./Images/tree.png)

V tomto stromu jsou větvích označené "NP" frází podstatné jméno.
Existuje několik takové věty: *I*, *nové hybridní automobilu*, *Bluetooth*, a *nové automobilu hybridní s Bluetooth*.

## <a name="phrase-types"></a>Typy fráze

| Štítek | Popis | Příklad: |
|-------|-------------|---------|
|ADJP   | Tvary přídavných jmen fráze | "proto článku neslušní" |
|ADVP   | Příslovce fráze | "Vymazat prostřednictvím" |
|CONJP  | Spojení fráze | "a také" |
|KŘEH   | Fragment, použít pro vstupy neúplný nebo fragmentary | "Důrazně doporučujeme..." |
|INTJ   | interjection | "Veselé Vánoce" |
|OBRÁZKŮ    | Seznam značek, včetně interpunkce | "#4)" |
|NAC    | Není A základní, slouží k určení zaměření frázi technická dokumentace |  "a pro dobrý pozornosti" v "můžete získat věcí a správné řešení" |
|NP | Podstatné jméno fráze | "tasty určené masopustní" |
|NX | Používá se v rámci určitých komplexní NPs k označení hlavičky| |
|PP | Prepositional fráze| "ve fondu" |
|PRN    | Shod| "(takže nazývané)" |
|PRT    | částice| "na" v "zkopírované out" |
|QP | Množství frázi (tj, komplexní měr nebo velikost) v rámci frázi podstatné jméno| "kolem $75" |
|KÓD DŮVODU REGISTRACE    | Snížené relativní klauzule.| "stále nepřeložené" v "Mnohé problémy s stále nepřeložené" |
|S  | Klauzule nebo věty. | "Toto je věty."
|SBAR   | Podřízená klauzule, často zaváděné subordinating spojení | "jako I vlevo" v "I hledá jak I zanechali."|
|SBARQ  | Přímý dotaz zaváděné shod slova nebo - fráze | "Co se bodem?" |
|SINV   | Obráceným deklarativní větu | "Současně byli jejich vědět." (Všimněte si, jak předmětu normálním "jejich" byla přesunuta do po příkaz "byli") |
|SQ | Obrácený Ano/Ne otázku nebo hlavní klauzule shod – otázky | "Se dostaly auto?" |
|UCP    | Na rozdíl od koordinované fráze| "malé, s chyby" (Všimněte si, jak conjoined přídavné jméno a heslo mezerami s "a")|
|VP | Příkaz fráze | "narazili woods" |
|WHADJP | Přídavných shod jmen fráze | "jak nepříjemně" |
|WHADVP | Fráze příslovce shod| "Pokud" |
|WHNP   | Fráze shod-podstatné jméno| "které určené", "kolik polévky"|
|WHPP   | Shod prepositional fráze| "v která země"|
|X  | Neznámý, neví nebo unbracketable.| první "na" v "... polévky" |


## <a name="specification"></a>Specifikace

Zde stromy použít S výrazy z [členem této Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
