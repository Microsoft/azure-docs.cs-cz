---
title: Analyzátor struktury v rozhraní API lingvistické Analysis názvů | Microsoft Docs
description: Zjistěte, jak lingvistické API analýzy využívá jeho strukturu pojmenování pro analyzátory flexibilní a přesnosti.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: 2729b7126e82862660fc8e1a995cc87ae996ea03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342427"
---
# <a name="analyzer-names"></a>Analyzátor názvy

Používáme poněkud složité pojmenování strukturu pro analyzátory umožňující obou flexibilitu na analyzátory a přesnost porozumět, co znamená název.
Název Analyzátor se skládá ze čtyř částí: ID, typ, specifikaci a implementace.
Níže jsou uvedeny roli jednotlivých součástí.

## <a name="id"></a>ID
Analyzátor jako první, má jedinečné ID; identifikátor GUID.
Tyto identifikátory GUID by se měl změnit relativně zřídka, ale jsou jediný způsob, jak jednoznačně popisují konkrétní analyzátor.

## <a name="kind"></a>Druh
Dále je každý analyzátor **druh**.
Definuje ve velmi široký podmínky typ analýzy, vrátí a jednoznačně měli definovat strukturu dat používá k reprezentaci této analýzy.
V současné době existují tři odlišné typy:
 - [Tokeny](Sentences-and-Tokens.md)
 - [POS značky](Pos-Tagging.md)
 - [Volebním stromu](constituency-parsing.md)

## <a name="specification"></a>Specifikace
V rámci daného typu ale jiné odborníky může Nesouhlasím na tom, jak by měly být analyzovány konkrétní jevu.
Na rozdíl od programovacích jazyků neexistuje žádná definice zrušte a přesně jak to by mělo být provedeno.

Například Představte si, že jsme se pokoušeli najít tokenů v anglické větu "Mu nebylo přejděte."
Zvažte především řetězec "nebylo".
Jeden možné interpretace je to by měl možné rozdělit do dvou tokenů: "nebyla" a "Ne".
Potom alternativní větu "mu není zmizelo" má stejnou sadu tokeny.
Další možností je říct, že by měl možné rozdělit na tokeny "nebyla" a "n't".
Druhé token nebyla za normálních okolností považována za slovo, ale tento přístup uchovává informace o prostor řetězce, který v některých případech může být užitečná.
Nebo možná této zmenšení by se měly zvažovat jednoho slova.

Bez ohledu na to se provádí které možnosti, že má být konzistentně k výběru.
Toto je přesněji role **specifikace**: k rozhodování o akcích by měla být znázornění správné.

Analyzátor výstupy můžete pouze poměrně porovnává se data, která odpovídá specifikaci stejné.

## <a name="implementation"></a>Implementace

Často existuje více modelů, které se pokoušejí o dosáhnout stejné výsledky, ale s jinou výkonové charakteristiky.
Jeden model může být rychlejší, když méně přesný; jiné provést jiný kompromis.

**Implementace** část názvu objektu analyzátor slouží k identifikaci tento typ informací, tak, aby uživatelé můžete vybrat nejvhodnější analyzátor pro své potřeby.
