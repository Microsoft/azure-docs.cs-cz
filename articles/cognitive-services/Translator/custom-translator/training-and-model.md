---
title: Co je školení a modelu? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Model je systém, který zajišťuje překlad pro konkrétní jazyk pár. Výsledek úspěšné školení je model. Při tréninku modelu, jsou požadovány trénovací datové sady, optimalizace datovou sadu a datovou sadu testování tři vzájemně se vylučující datových sad.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: bd4921cb959e3ea2e893c6837fb47792d3585ca9
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220123"
---
# <a name="what-are-trainings-and-models"></a>Co jsou školení a modely?

Model je systém, který zajišťuje překlad pro konkrétní jazyk pár.
Výsledek úspěšné školení je model. Při tréninku modelu, tři vzájemně se vylučující datové sady jsou požadovány: trénovací datové sady, optimalizace datové sady a testování datové sady. Slovník dat lze také zadat.

Pokud jen Cvičná data poskytuje při zařazení do fronty školení, bude automaticky sestavit Translator vlastní, ladění a testování datových sad. Bude vyloučit 5 000 vět z trénovacích dat a 2 500 každý z nich k sestavování ladění a testování sad.

## <a name="training-dataset-for-custom-translator"></a>Trénovací datové sady pro vlastní Translator

Vlastní překladač používají dokumenty, které jsou součástí trénovací sada jako základ pro vytváření modelu. Během provádění školení věty, které jsou k dispozici v těchto dokumentech jsou zarovnána (nebo spárované). Můžete využít svobod v sestavování sady dokumentů školení. Můžete zahrnout dokumenty, které si myslíte, že se dotýká podle relevance v jednom modelu. Znovu vyloučit, pokud je v jiném zobrazení dopadů v [BLEU (Understudy dvojjazyčné vyhodnocení) skóre](what-is-bleu-score.md). Jak dlouho, dokud je ladění sady a testovací sady konstanta, bez obav experimentovat s složení trénovací sady. Tento přístup je účinný způsob, jak upravit kvalitu překladu systému.

Můžete spustit více školení v rámci projektu a porovnat [BLEU skóre](what-is-bleu-score.md) přes všechny tréninkových spuštění. Pokud spouštíte více školení pro porovnání, zajistěte stejnou ladění / pokaždé, když je zadána testovací data. Také si nezapomeňte také zkontrolovat ručně v výsledky ["Testování"](how-to-view-system-test-results.md) kartu.

## <a name="tuning-dataset-for-custom-translator"></a>Optimalizace datové sady pro vlastní Translator

Paralelní dokumenty, které jsou zahrnuté v této sadě používají vlastní překladač vyladit překladový systém pro dosažení optimálních výsledků.

Ladění sady se používá během cvičení a upravte všechny parametry a systému překladu do optimálních hodnot váhu. Zvolte vaši ladění pečlivě nastavit: optimalizace sada by měla vystihovat obsah dokumentů, které chcete přeložit v budoucnu. Ladění sady má hlavní vliv na kvalitu překladu vytvořen. Ladění umožňuje překlad systém mohl zajistit překlady, které jsou uživatelům nejblíže na ukázky, které zadáte v ladění datové sadě. Není nutné více než 2500 věty jako ladění sady. Pro optimální překlad kvality se doporučuje ručně vyberte sadu ladění zvolením nejreprezentativnější výběr vět.

Při vytváření sady optimalizace, zvolte věty, které mají smysl a reprezentativní délku budoucí vět, které očekáváte, že pro převod. Měli byste zvolit také věty obsahující slova a slovní spojení, které chcete přeložit v přibližné distribuci, která očekáváte, že v budoucnu překlady. V praxi vytvoří věty délku 8 až 18 slova nejlepších výsledků dosáhnete, protože tyto věty obsahují dostatečný kontext a zobrazit důležitý poskytující délkou frázi, která je důležité, aniž by byla příliš složitá.

Dobrý popis typu věty a použít v sadě ladění je prose: skutečný fluent věty. Buňky tabulky, není básních, ne seznam věcí, nejen interpunkční znaménka ani čísla ve větě – regulární jazyka.

Pokud po ručním výběru ladění datovou sadu, by neměl mít některý z stejné věty jako trénovací a testovací data. Ladění sady má významný dopad na kvalitu překladů – pečlivě vět.

Pokud si nejste jisti, co se má vybrat pro ladění sady, stačí vybrat sadu školení a nechat vlastní překladač vyberte sady optimalizace za vás. Pokud umožníte vlastní překladač zvolit sadu ladění automaticky ji použije podmnožinu náhodné věty z vašich dokumentů dvojjazyčné školení a vyloučit tyto věty z školicí materiály, samotného.

## <a name="testing-dataset-for-custom-translator"></a>Testovací datové sady pro vlastní Translator

Paralelní dokumenty, které jsou součástí sady testování se používají k výpočtu skóre BLEU (Understudy dvojjazyčné vyhodnocení). Toto skóre udává kvalitu překladu systému. Toto skóre skutečně zjistíte, jak překladů provádí překlad systému vyplývajících z této školicí odpovídalo vět referenční datové sady testů.

Skóre BLEU je měření rozdílů mezi automatický překlad a překladu odkazu. Jeho hodnotu od 0 do 100. Hodnota 0 označuje, že jedno slovo odkazu se zobrazí v překladu. Skóre 100 označuje, že automatický překlad přesně odpovídá odkaz: téhož slova je přesně stejné pozici. Skóre, které obdržíte je průměr BLEU skóre pro všechny věty sady testování.

Testovací sada by měla obsahovat paralelní dokumenty, kde jsou věty cílový jazyk nejvhodnější překlady odpovídající věty Zdrojový jazyk v páru. Můžete chtít použít stejná kritéria, které jste použili k vytvoření sady pro optimalizaci. Sady testování však nemá žádný vliv na kvalitu překladu systému. Používá se jenom pro vygenerování skóre BLEU za vás a pro nic jiného.

Není nutné více než 2 500 věty jako sady testování. Když necháte systém automaticky zvolte testovací sadu, bude používat podmnožinu náhodné věty z vašich dokumentů dvojjazyčné školení a vyloučit tyto věty z školicí materiály, samotného.

Můžete zobrazit vlastní překlady testovací sady a jejich porovnání překlady podle testovací sady, tak, že přejdete na kartu testu v rámci modelu.
