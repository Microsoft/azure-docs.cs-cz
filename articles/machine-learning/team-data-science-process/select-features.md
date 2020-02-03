---
title: Výběr funkcí v vědecké zpracování týmových dat
description: Vysvětluje účel výběr součástí a poskytuje příklady jejich role v procesu rozšíření dat služby machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716685"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Výběr funkcí ve vědeckém zpracování týmových dat (TDSP)
Tento článek vysvětluje účely výběr součástí a poskytuje příklady jejich rolí v procesu rozšíření dat služby machine learning. Tyto příklady jsou vykreslovány vedle z Azure Machine Learning Studio.

Inženýrské a výběr funkcí je jednou z vědeckých procesů týmových dat (TDSP), které jsou uvedené v článku [co je vědecký proces týmových dat?](overview.md). Technologie a výběr funkcí jsou části kroku **vývoj funkcí** TDSP.

* **inženýrské funkce**: Tento proces se pokusí vytvořit další relevantní funkce z existujících nezpracovaných funkcí v datech a zvýšit prediktivní sílu na školicí algoritmus.
* **Výběr funkcí**: Tento proces vybere podmnožinu klíčových prvků pro původní data v pokusu snížit dimenzionální problém při výuce.

Obvykle se pro generování dalších funkcí používá **inženýr funkcí** a pak se provede krok **výběru funkcí** , který eliminuje nepodstatné, redundantní nebo vysoce korelační funkce.

## <a name="filter-features-from-your-data---feature-selection"></a>Funkce filtru z vašich dat – výběr funkcí
Výběr funkcí lze použít pro úlohy klasifikace nebo regrese. Cílem je vybrat podmnožinu funkcí z původní datové sady, který snížit jeho rozměry pomocí minimální sada funkcí pro reprezentaci maximální množství variance v datech. Tato podmnožina funkcí se používá pro trénování modelu. Výběr funkce má dva hlavní účely.

* Výběr funkce nejprve často zvýší přesnost klasifikace odstraněním irelevantní, redundantní nebo vysoce korelují funkce.
* Za druhé snižuje počet funkcí, což zajišťuje efektivnější procesu trénování modelu. Efektivita je důležitá pro informace, které jsou nákladné pro výuku, jako je například podpora vektorových počítačů.

I když se výběr funkce hledání a snížit počet funkcí v datové sadě využívají k tréninku modelu, to není odkazuje termín "snížení". Funkce Výběr metody extrahování podmnožinu původní funkce v datech beze změny.  Metody pro snížení dimenzionalitu využívat inženýrství funkce, které se dají se transformovat původní funkce a proto je upravit. Příklady metod dimenzionalitu snížení zahrnují hlavní komponenty analýzy, analýzy canonical korelace a jednotném rozložené hodnotu.

Mimo jiné široce použité druh metody výběru funkcí v rámci pod dohledem se nazývá "výběr na základě filtru funkce". Vyhodnocením korelace mezi jednotlivé funkce a cílový atribut platí tyto metody statistické míru přiřadit skóre pro jednotlivé funkce. Funkce jsou pak seřazené podle skóre, které slouží k nápovědě nastavení prahové hodnoty pro udržování nebo odstranění konkrétní funkce. Příklady statistické míry použít v těchto metodách: korelace, vzájemné informace a ve čtverci test chí osoby.

V Azure Machine Learning Studio jsou moduly, které jsou k dispozici pro výběr funkcí. Jak je znázorněno na následujícím obrázku, tyto moduly zahrnují [Výběr funkcí založených na filtrech][filter-based-feature-selection] a [Fisher lineární analýzu Discriminant][fisher-linear-discriminant-analysis].

![Funkce výběru modulů](./media/select-features/feature-Selection.png)

Vezměte v úvahu například použití modulu [výběru funkcí založených na filtrech][filter-based-feature-selection] . Pro usnadnění práce pokračujte v používání příklad dolování textu. Předpokládejme, že chcete sestavit regresní model poté, co je vytvořena sada funkcí 256 pomocí modulu [hashování funkcí][feature-hashing] a že proměnná odpovědi je "Sloupe", která obsahuje knihu hodnocení v rozsahu od 1 do 5. Nastavením "Funkce bodování metoda" na "Pearson korelace", "cílový sloupec" "Sloupec1" a "Počet požadovaných funkcí" na 50. Pak [Výběr funkce založený na filtrech][filter-based-feature-selection] vytvoří datovou sadu obsahující 50 funkcí společně s atributem target "Sloupe". Následující obrázek znázorňuje tok tento experiment a vstupní parametry:

![Vlastnosti modulu výběru funkcí založených na filtrech](./media/select-features/feature-Selection1.png)

Následující obrázek znázorňuje výsledné datové sady:

![Výslednou sadu dat pro modul filtrovat na základě výběru funkce](./media/select-features/feature-Selection2.png)

Jednotlivé funkce vypočítává se skóre na základě na Pearson korelace mezi samostatně a atribut target "Sloupec1". Funkce s nejvyšší skóre, které jsou udržovány.

Odpovídající skóre vybrané funkce jsou vidět na následujícím obrázku:

![Skóre pro modul filtrovat na základě výběru funkce](./media/select-features/feature-Selection3.png)

Použitím tohoto modulu [výběru funkcí založeného na filtrech][filter-based-feature-selection] se vybere 50 z 256 funkcí, protože mají nejvíc korelující funkce s cílovou proměnnou "Sloupe" na základě metody bodování "Pearsonova Correlation".

## <a name="conclusion"></a>Závěr
Vytváření funkcí a výběr funkcí jsou dvě běžně navržené a vybrané funkce zvyšují efektivitu školicího procesu, který se pokouší extrahovat informace o klíčích obsažených v datech. Také zvyšují power tyto modely přesně klasifikovat vstupních dat a více robustní předvídat výstupy, které vás zajímají. Aby studijním výpočetně tractable můžete také kombinovat funkce konstrukce a výběr. Dělá to tak, že rozšíření a pak snížení počtu funkce potřebné pro nakalibrovat pro ni nebo trénování modelu. Funkce vybrané pro trénování modelu matematicky vzato jsou minimální sadu nezávislých proměnných, které popisují vzory v datech a pak úspěšně předpovědět výsledky.

Není vždy nutně provést výběr součástí inženýrství nebo funkce. Určuje, zda je potřeba nebo Ne, závisí na data shromažďovaná, algoritmus vybrané a cílem tohoto experimentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

