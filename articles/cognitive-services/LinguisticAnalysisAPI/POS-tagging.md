---
title: Část řeči označování analýzou lingvistické rozhraní API | Microsoft Docs
description: Zjistěte, jak část řeči označení ve službě Microsoft kognitivní identifikuje kategorie nebo slovní jednotlivých slov textu.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 09/27/2016
ms.author: lesun
ms.openlocfilehash: 90fd5b05c2dabdac88c6c8da288ab629177be38d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082634"
---
# <a name="part-of-speech-tagging"></a>Část řeči označování

## <a name="background-and-motivation"></a>Pozadí a motivace

Jakmile textový má rozdělené na věty a tokeny, dalším krokem analýzy je identifikace kategorie nebo část řeči jednotlivých slov.
Patří mezi ně kategorií jako *podstatné jméno* (obvykle představující osob, míst, věcí, nápady, atd.) a *příkaz* (obvykle reprezentující akce změny stavu atd. Pro některé slova, je jednoznačné část-rozpoznávání řeči (například *quagmire* je ve skutečnosti jenom podstatné jméno), ale pro mnoho dalších je obtížné zjistit.
*Tabulka* může být místní, kde nacházejí (nebo 2-D rozložení čísla), ale můžete také "tabulky se zabývat".

## <a name="list-of-part-of-speech-tags"></a>Seznam značek, část řeči

| Značka | Popis | Příklad slova |
|-----|-------------|---------------|
| $ | dolar | $ |
| \`\` | levé uvozovky | \` \`\` |
| '' | uzavírací uvozovky | ' '' |
| ( | levé závorky | ( [ { |
| ) | kulaté závorky | ) ] } |
| , | čárkami | , |
| -- | pomlčka | -- |
| . | věty ukončovací znak | . ! ? |
| : | středníkem nebo třemi tečkami | : ; ... |
| Kopie | spojení, koordinace | a, ale nebo ještě|
| CD | číslo, mohutnosti | devět 1980 20 "96 |
| DT | determiner |všechny obou ani|
| EX | existenční zde | existuje |
| FW | cizí aplikace word | enfant terrible hoi polloi je ne sais quoi |
| V | mezerami nebo subordinating spojení| Pokud se vyskytuje při jestli v uvnitř |
| J. J. | přídavných jmen nebo číslicí, pořadí | deváté poměrně execrable Multimodální |
| JJR | přídavných jmen, srovnávací | lépe rychlejší levnější |
| JJS | přídavných jmen superlative | nejlepší nejrychlejších nejlevnější | 
| LS | značka položky seznamu | (a) (b) 1 2 A B A. B. |
| MD | modální pomocné | můžete může se bude může může měli by mělo být |
| NN | podstatné jméno, běžné, singulární nebo velkokapacitní | určené peníze bot |
| NNP | správné, jednotném čísle podstatné jméno, | Kennedy Roosevelt Chicagu Weehauken |
| NNPS | podstatné jméno, správné, množném čísle | Springfields keře |
| NNS | podstatné jméno, běžné, množném čísle | pole části myši |
| PDT (Tichomořský letní čas) | před determiner | všechny obou půl mnoho poměrně takové že to |
| POS | genitive značky | ' je |
| ZÁSADY REPLIKACE HESEL | zastupovat, osobní | Jana si ho I jsme se vám |
| ZÁSADY REPLIKACE HESEL$ | zastupovat přivlastňovacího pádu | Mamince jeho jeho Moje naše jejich vaší |
| RB | příslovce | klinicky pouze |
| RBR | příslovce, srovnávací | Další gloomier grander graver větší grimmer těžší přísnější zdravějších těžší vyšší ale větší novější zeštíhlen delší méně perfektně menší lonelier delší hlasitější nižší více... |
| RBS | příslovce superlative | nejlepší největších bluntest nejdřívější nejvíce první nejvzdálenější nejtěžší heartiest nejvyšší největší nejmenší méně většinu nejbližší druhý tightest nejhorší |
| RP | částice | na vypnuto. až se o |
| SYM | Symbol | % & |
| DO | "do" jako mezerami nebo infinitive značky | na |
| UH | interjection | UH Veselé Vánoce Hele hello |
| VB | příkaz, základní formulář | Udělte přiřadit chodu |
| VBD | příkaz, minulost | Dal přiřazené flew |
| VBG | příkaz, přítomen jiném čase či osobě nebo gerund | poskytnutí přiřazení Průlet |
| VBN | příkaz, minulosti jiném čase či osobě | zadané přiřazené vede |
| VBP | příkaz, přítomném, není 3. osoba singulární | Udělte přiřadit chodu |
| VBZ | příkaz, přítomném, 3. osoba singulární | poskytuje přiřadí čarou |
| WDT | Determiner SHOD | jaké který |
| WEBOVÉ ČÁSTI | Zastupovat SHOD | kdo kterého |
| WP$ | SHOD zastupovat přivlastňovacího pádu | jejichž |
| WRB | Příslovce shod | Jak ale vždy, když kde |

## <a name="specification"></a>Specifikace

Jako tokenizaci, spoléháme na specifikaci z [členem této Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
