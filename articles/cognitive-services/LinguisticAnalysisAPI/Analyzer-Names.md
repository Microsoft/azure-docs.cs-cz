---
title: Analyzátor pojmenování strukturu – rozhraní API pro jazykovou analýzu
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak strukturu pojmenování lingvistické analýzy analyzátor rozhraní API umožňuje flexibilitu a přesnost.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: a998bdf32be948448131ea12db1b7d4204e6722d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127461"
---
# <a name="analyzer-names"></a>Názvy analyzátorů

Používáme poměrně složitá struktura názvů analyzátory pro obě flexibilitu v analyzátory a přesnost porozumět tomu, co znamená název.
Názvy analyzátorů se skládají ze čtyř částí: ID, typ, specifikace a implementaci.
Role jednotlivých komponent je definována níže.

## <a name="id"></a>ID
Analyzátor jako první, má jedinečné ID; identifikátor GUID.
Tyto identifikátory GUID by se měla změnit poměrně zřídka, ale je jediný způsob, jak jednoznačně popisují konkrétní analyzátor.

## <a name="kind"></a>Druh
V dalším kroku je každý analyzátoru **druh**.
Definuje v velmi široké podmínky typu analýzu vrátil a jednoznačně by měl definovat strukturu dat používá k reprezentování analýzy.
V současné době jsou tři odlišné typy:
 - [Tokeny](Sentences-and-Tokens.md)
 - [Značky POS](Pos-Tagging.md)
 - [Složková stromu](constituency-parsing.md)

## <a name="specification"></a>Specifikace
V rámci daného typu ale jiné odborníků může Nesouhlasím na tom, jak by měl být analyzován určitého jevu.
Na rozdíl od programovacích jazyků neexistuje žádná definice jasné a přesně jak to by mělo být provedeno.

Například Představte si, že jsme se pokoušeli najít tokeny v angličtině větu "Mu nepovedlo přejděte."
Zvažte především, řetězec "neměli".
Jeden možný výkladu je, že to byste rozdělit do dvou tokeny: "nebyl" a "not".
Potom alternativní větu "mu není najdu" má stejnou sadu tokenů.
Další možností je říct, že by měl být rozdělit na tokeny "nebyl" a "Nezobrazovat".
Druhá možnost tokenu by normálně považovat za slova, ale tento přístup uchovává informace o surface řetězec, který může být někdy užitečné.
Nebo možná tohoto snížení by měl být jediné slovo.

Bez ohledu na to se provádí výběru, že podle výběru má být provedena konzistentně.
To je přesně role **specifikace**: rozhodování, co by měl být správnou reprezentace.

Analyzátor výstupy můžete pouze poměrně porovnání na data, která odpovídá specifikaci stejné.

## <a name="implementation"></a>Implementace

Často existuje více modelů, které se snaží dosáhnout stejných výsledků, ale jiné výkonové charakteristiky.
Jeden model může být rychlejší však méně přesné; jiné by mohlo způsobit nepoužitelnost různých kompromis.

**Implementace** část názvu Analyzátor se používá k identifikaci tohoto typu informací, tak, aby uživatelé můžete vybrat nejvhodnější analyzátor pro jejich potřeby.
