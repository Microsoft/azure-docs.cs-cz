---
title: Označování částí řeči – rozhraní API pro jazykovou analýzu
description: Zjistěte, jak označování částí řeči v rozhraní API pro jazykovou analýzu identifikuje kategorie nebo slovní každého slova textu.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 41383b5bbad0eb5c32b98c57f3ea35e6018993e8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218117"
---
# <a name="part-of-speech-tagging"></a>Označování částí řeči

> [!IMPORTANT]
> Dne 9. srpna 2018 došlo k vyřazení jazykové analýzy ve verzi Preview z provozu. Ke zpracování a analýze textu doporučujeme používat [moduly analýzy textu služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

## <a name="background-and-motivation"></a>Na pozadí a motivace

Jakmile text má rozdělené na věty a tokeny, dalším krokem analýzy je k identifikaci kategorie nebo část řeči jednotlivých slov.
Patří mezi ně kategoriích, jako je *podstatné jméno* (obecně představující lidé, místa, věci, nápady, atd.) a *příkaz* (obecně představující akce, změny stavu a další. Pro některé slova je jednoznačný část – rozpoznávání řeči (například *quagmire* je pouze podstatné jméno), pro řadu dalších je však obtížné zjistit.
*Tabulka* může být místo, kde nacházejí (nebo 2D rozložení čísla), ale můžete také "tabulky diskusi".

## <a name="list-of-part-of-speech-tags"></a>Seznam značek částí řeči

| Značka | Popis | Příklad slova |
|-----|-------------|---------------|
| $ | dolar | $ |
| \`\` | uvozovky | \` \`\` |
| '' | uzavírací uvozovky | ' '' |
| ( | Levá závorka | ( [ { |
| ) | Pravá závorka | ) ] } |
| ,  | Čárka | ,  |
| -- | pomlčka | -- |
| . | ukončovací znak větu | . ! ? |
| : | dvojtečka nebo tlačítko se třemi tečkami | : ; ... |
| Kopie | spojení, že koordinuje | a, ale nebo ještě|
| CD | číslo, mohutnosti | devět 1980 20 "96 |
| DT | determiner |všech obou ani|
| EX | existenční zde | existuje |
| FW | cizí slova | enfant terrible hoi polloi je ne sais quoi |
| IN | mezerami nebo podřazování spojení| Pokud se vyskytuje na, jestli se v uvnitř |
| JJ | přídavného jména nebo číslo, pořadové | devátý poměrně execrable Multimodální |
| JJR | srovnávací přídavného jména | lepší rychleji levnější |
| JJS | přídavného jména superlative | nejlepší nejrychlejší nejlevnější |
| LS | značka položky seznamu | (a) (b) 1 2 A B A. B. |
| MD | pomocné modální okno | můžete může se bude může pravděpodobně by měl by mělo být |
| NN | podstatné jméno, běžné, jednotném čísle nebo hmotnost | bot brambory peníze |
| NNP | správné, jednotném čísle podstatné jméno, | Kennedy Roosevelt Chicago Weehauken |
| NNPS | správné, plural podstatné jméno, | Springfields keře |
| NNS | běžné, plural podstatné jméno, | kusů mice pole |
| PDT (Tichomořský letní čas) | pre-determiner | všechny obou polovině mnoho poměrně takové že to |
| POS | genitive značky | ". |
| PRP | zastupovat osobní | Jana si ho můžu jsme jejich můžete |
| ZÁSADY REPLIKACE HESEL$ | zastupovat přivlastňovacího pádu | Acyklický jeho jeho Moje naše jejich vaší |
| RB | příslovce | klinicky pouze |
| RBR | příslovce srovnávací | Další gloomier grander graver větší grimmer obtížnější přísnější znamenala těžší vyšší však větší novější štíhlejší delší méně – dokonale menší lonelier delší hlasitější nižší více... |
| RBS | příslovce superlative | nejlepší největší bluntest nejdřívější nejvíce první nejvzdálenější nejtěžší heartiest nejvyšší největší nejméně méně většinu nejbližší druhé tightest nejhorší |
| RP | částicový | vypnutý nahoru o navýšení kapacity |
| SYM | Symbol | % & |
| DO | "do" jako mezerami nebo infinitive značky | na |
| UH | interjection | uh hooray howdy hello |
| VB | příkaz základního formuláře | umožňují přiřadit za běhu |
| VBD | příkaz minulý čas | přiřadil přiřazené městečku |
| VBG | příkaz, k dispozici jiném čase či osobě nebo gerund | poskytne Průlet přiřazení |
| VBN | příkaz minulosti jiném čase či osobě | Zadaný přiřazené předávány |
| VBP | příkaz, přítomný čas, nikoli 3. osoba singulární | umožňují přiřadit za běhu |
| VBZ | příkaz, přítomný čas, 3. osoba singulární | poskytuje přiřadí čarou |
| WDT | WH-determiner | jaké který |
| WP | WH-pronoun | kdo kterému |
| WP$ | CO zastupovat přivlastňovacího pádu | jehož |
| WRB | Co příslovce | Jak ale pokaždé, když kde |

## <a name="specification"></a>Specifikace

Jako u Tokenizace, spoléháme na specifikaci od [diskutují Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
