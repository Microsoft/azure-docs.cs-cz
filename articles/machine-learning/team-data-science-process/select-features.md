---
title: Výběr funkcí v vědecké zpracování týmových dat
description: Vysvětluje účel výběr součástí a poskytuje příklady jejich role v procesu rozšíření dat služby machine learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: de8070906f7b2470378fb631f2e94a96b4a2960d
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138649"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Výběr funkcí ve vědeckém zpracování týmových dat (TDSP)
Tento článek vysvětluje účely výběr součástí a poskytuje příklady jejich rolí v procesu rozšíření dat služby machine learning. Tyto příklady jsou vykreslovány vedle z Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Konstrukce a výběr funkcí je jednou ze součástí nástroje zpracování týmových dat vědy (TDSP) uvedených v článku [co je vědecké zpracování týmových dat?](overview.md). Vytváření funkcí a výběru jsou části **vývoj funkcí** krok TDSP.

* **konstrukce funkcí**: Tento proces pokusí vytvořit další odpovídající funkce z existující funkce nezpracovaných dat a ke zvýšení předpovídat algoritmu učení.
* **Výběr funkcí**: Tento proces vybere klíče podmnožinu funkcí původní data za účelem snížení dimenzionalitu problém školení.

Obvykle **konstruování** se použije první generovat další funkce a pak **výběr funkcí** krok se provádí za účelem odstranění irelevantní, redundantní nebo vysoce korelační funkce.

## <a name="filter-features-from-your-data---feature-selection"></a>Funkce filtru z vašich dat – výběr funkcí
Výběr funkcí je proces, který se běžně používají pro tvorbu cvičných datových sad pro prediktivní modelování úkolů, například klasifikační nebo regresní. Cílem je vybrat podmnožinu funkcí z původní datové sady, který snížit jeho rozměry pomocí minimální sada funkcí pro reprezentaci maximální množství variance v datech. Tato podmnožina funkcí se používá pro trénování modelu. Výběr funkce má dva hlavní účely.

* Výběr funkce nejprve často zvýší přesnost klasifikace odstraněním irelevantní, redundantní nebo vysoce korelují funkce.
* Za druhé snižuje počet funkcí, což zajišťuje efektivnější procesu trénování modelu. Efektivita je obzvláště důležité pro inteligentních algoritmů, které je vždycky tak moct trénovat, jako jsou počítače vektorové podpory.

I když se výběr funkce hledání a snížit počet funkcí v datové sadě využívají k tréninku modelu, to není odkazuje termín "snížení". Funkce Výběr metody extrahování podmnožinu původní funkce v datech beze změny.  Metody pro snížení dimenzionalitu využívat inženýrství funkce, které se dají se transformovat původní funkce a proto je upravit. Příklady metod dimenzionalitu snížení zahrnují hlavní komponenty analýzy, analýzy canonical korelace a jednotném rozložené hodnotu.

Mimo jiné široce použité druh metody výběru funkcí v rámci pod dohledem se nazývá "výběr na základě filtru funkce". Vyhodnocením korelace mezi jednotlivé funkce a cílový atribut platí tyto metody statistické míru přiřadit skóre pro jednotlivé funkce. Funkce jsou pak seřazené podle skóre, které slouží k nápovědě nastavení prahové hodnoty pro udržování nebo odstranění konkrétní funkce. Příklady statistické míry použít v těchto metodách: korelace, vzájemné informace a ve čtverci test chí osoby.

V Azure Machine Learning Studio jsou moduly, které jsou k dispozici pro výběr funkcí. Jak je znázorněno na následujícím obrázku, patří tyto moduly [výběr součástí na základě filtru] [ filter-based-feature-selection] a [lineární analýza Discriminant Fisher] [ fisher-linear-discriminant-analysis].

![Funkce výběru modulů](./media/select-features/feature-Selection.png)

Zvažte například použití [výběr součástí na základě filtru] [ filter-based-feature-selection] modulu. Pro usnadnění práce pokračujte v používání příklad dolování textu. Předpokládejme, že chcete sestavit regresní model vytvořený sadou funkcí 256 prostřednictvím [Hashování] [ feature-hashing] modulu a že je proměnná odpovědi "Sloupec1", který obsahuje knihy zkontrolujte hodnocení od 1 do 5. Nastavením "Funkce bodování metoda" na "Pearson korelace", "cílový sloupec" "Sloupec1" a "Počet požadovaných funkcí" na 50. Potom modulu [výběr součástí na základě filtru] [ filter-based-feature-selection] vytvoří datovou sadu obsahující 50 funkce společně s cílový atribut "Sloupec1". Následující obrázek znázorňuje tok tento experiment a vstupní parametry:

![Vlastnosti modulu výběr součástí na základě filtru](./media/select-features/feature-Selection1.png)

Následující obrázek znázorňuje výsledné datové sady:

![Výslednou sadu dat pro modul filtrovat na základě výběru funkce](./media/select-features/feature-Selection2.png)

Jednotlivé funkce vypočítává se skóre na základě na Pearson korelace mezi samostatně a atribut target "Sloupec1". Funkce s nejvyšší skóre, které jsou udržovány.

Odpovídající skóre vybrané funkce jsou vidět na následujícím obrázku:

![Skóre pro modul filtrovat na základě výběru funkce](./media/select-features/feature-Selection3.png)

Použitím to [výběr součástí na základě filtru] [ filter-based-feature-selection] modulu, 50 z celkového počtu 256 funkce jsou vybrané, protože mají největší korelační funkcí Cílová proměnná "Sloupec1" podle hodnocení – metoda "Pearson korelace".

## <a name="conclusion"></a>Závěr
Vytváření funkcí a výběr součástí jsou dva běžně analyzovány a vybrané funkce zvýšit účinnost procesu trénování, která se pokusí extrahovat informace o klíči obsažena v datech. Také zvyšují power tyto modely přesně klasifikovat vstupních dat a více robustní předvídat výstupy, které vás zajímají. Aby studijním výpočetně tractable můžete také kombinovat funkce konstrukce a výběr. Dělá to tak, že rozšíření a pak snížení počtu funkce potřebné pro nakalibrovat pro ni nebo trénování modelu. Funkce vybrané pro trénování modelu matematicky vzato jsou minimální sadu nezávislých proměnných, které popisují vzory v datech a pak úspěšně předpovědět výsledky.

Není vždy nutně provést výběr součástí inženýrství nebo funkce. Určuje, zda je potřeba nebo Ne, závisí na data shromažďovaná, algoritmus vybrané a cílem tohoto experimentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

