---
title: Složková analýza – rozhraní API pro jazykovou analýzu
titlesuffix: Azure Cognitive Services
description: Další informace o způsobu, jakým složková analýza, označované také jako "frázi parsování struktura" identifikuje fráze v textu.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 89832f2d936a08df8b6f9e846c3dd4a5665c06a4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238620"
---
# <a name="constituency-parsing"></a>Analýza konstituentů

> [!IMPORTANT]
> Pro jazykovou analýzu ve verzi preview byla vyřazena z provozu, 9. srpna 2018. Doporučujeme používat [moduly analýzy textu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pro zpracování textu a analýzy.

Cílem složková analýza (označované také jako "struktura fráze parsování") je k identifikaci fráze v textu.
To může být užitečné při extrahování informací z textu.
Zákazníkům může být vhodné k vyhledání názvů funkcí nebo klíčových frází z velkých objemů textu a zobrazíte modifikátorů a vlastností akce kolem každé takové frázi.

## <a name="phrases"></a>Fráze

K prostudoval *frázi* je víc než jenom posloupností slov.
Být fráze, musí skupina slov setkávají, aby se přehrát určité role ve větě.
Tuto skupinu slov můžete přesunout najednou nebo nahradit jako celek, a věty by měla zůstat fluent a gramatické.

Vezměte v úvahu větu

> Chci najít nové hybridní automobil s připojením Bluetooth.

Fráze podstatné jméno obsahuje tuto větu: "nové hybridní automobil s připojením Bluetooth,".
Jak jsme si vědomi, že se jedná o nějakou frázi?
Jsme (poněkud poetically) revize věty tak, že celá fráze do popředí:

> Nová hybridní automobil s připojením Bluetooth, které má být nalezena.

Nebo může nahradíme danou frázi s frází podobně jako funkce a význam, jako je "nápadité nové car":

> Chci najít nápadité nové auto.

Pokud místo výběru různé podmnožiny slova, tyto úlohy nahrazení by mohlo dojít k neobvyklé či nečitelných věty.
Zvažte, co se stane, když jsme přesunout "najít nové" do popředí:

> Najdete nové, které chci hybridní automobil s připojením Bluetooth.

Výsledky jsou velmi obtížné si a porozuměli jim.

Cílem je analyzátor je najít všechny takové věty.
Zajímavé v přirozeném jazyce věty mají být vnořena do jiné.
Přirozené vyjádření tyto věty je stromové struktury, jako je následující:

![Strom](./Images/tree.png)

Ve stromu větve označené "NP" jsou věty podstatné jméno.
Existuje několik takových frází: *můžu*, *nové hybridní automobile*, *Bluetooth*, a *nové hybridní auto s Bluetooth*.

## <a name="phrase-types"></a>Typy fráze

| Štítek | Popis | Příklad: |
|-------|-------------|---------|
|ADJP   | Tvary přídavných jmen fráze | "proto hrubé" |
|ADVP   | Příslovce fráze | "clear prostřednictvím" |
|CONJP  | Fráze spojení | "i" |
|KŘEH   | Fragment pro neúplné nebo fragmentary vstupy | "Důrazně doporučujeme..." |
|INTJ   | interjection | "Radostných" |
|OBRÁZKŮ    | Seznam značek, včetně interpunkce | "#4)" |
|NAC    | Není A základní, slouží k určení nastavení rozsahu bez rozložený fráze |  "a pro mnoho" v "získáte věci a dobrého řešení" |
|NP | Fráze podstatné jméno | "tasty brambory masopustní" |
|NX | V některých složitých server NPs používá k označení hlavičky| |
|STR | Prepositional fráze| "ve fondu" |
|PRN    | Kulatých závorek| "(tedy volána)" |
|PRT    | částicový| "out" v "zkopírované si" |
|QP | Množství fráze (například komplexní míry/Částka) v rámci podstatné jméno fráze| "around $75" |
|KÓD DŮVODU REGISTRACE    | Snížení relativní klauzule.| "stále nevyřešené" v "mnoho problémů stále nevyřešené" |
|S  | Věty nebo klauzuli. | "Toto je větu."
|SBAR   | Klauzule podřízené, často zavedené subordinating spojení | "Jak mám left" v "I trochu jak mi zbývá."|
|SBARQ  | Přímý dotaz zavedených v co slova nebo - fráze | "Jak se bod?" |
|SINV   | Obráceným deklarativní větu | "Současně tato nabídka neunikne jejich." (Všimněte si, jak normální předmět "," byl přesunut do po operaci "byly") |
|SQ. | Převrátí Ano/Ne otázku nebo klauzuli hlavní otázky shod | "Se dostaly auta?" |
|UCP    | Na rozdíl od koordinovaného fráze| "malý a s chybami" (Všimněte si, jak conjoined přídavné a frází mezerami s "a")|
|VICEPREZIDENT | Příkaz fráze | "narazili lese" |
|WHADJP | Co přídavného jména fráze | "jak nepříjemně" |
|WHADVP | Co příslovce fráze| "when" |
|WHNP   | Co-podstatné jméno fráze| "které brambory", "kolik polévky"|
|WHPP   | Co prepositional fráze| "v zemi"|
|X  | Neznámý stav nebo unbracketable.| první "the" v "... polévky" |


## <a name="specification"></a>Specifikace

Stromy tady použít výrazy z [diskutují Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
