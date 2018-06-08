---
title: Funkce výběr v procesu vědecké účely dat Team | Microsoft Docs
description: Vysvětlující účel výběr funkce a příklady jejich role v procesu dat. vylepšení nástroje machine learning.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 2504d14bad3a7a3b3845e880e40034ef7e4c126b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838625"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Výběr funkcí ve vědeckém zpracování týmových dat (TDSP)
Tento článek popisuje účely výběr funkce a obsahuje příklady jeho role v proces vylepšení dat machine learning. Tyto příklady jsou vykreslovány z Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Technici a výběr funkcí je jednou ze součástí z Team Data vědecké účely procesu (TDSP) uvedených v článku [co je proces Team dat. vědecké účely?](overview.md). Funkce inženýrství a výběr jsou součástí **vyvíjet funkce** krok TDSP.

* **konstruování**: Tento proces se pokusí vytvořit další relevantní funkce z existující nezpracovaná funkce v datech a ke zvýšení výkonu prediktivní k algoritmus učení.
* **funkce Výběr**: Tento proces vybere klíče podmnožinu funkcí, původní data ve snaze snížit dimenzionalitu problému školení.

Za normálních okolností **konstruování** se použije první ke generování další funkce a potom **funkci Výběr** krok se provádí eliminovat důležité, redundantní nebo vysoce korelační funkce.

## <a name="filter-features-from-your-data---feature-selection"></a>Funkce filtru z vašich dat – Výběr funkce
Výběr funkce je proces, který se běžně použije pro tvorbu datových sad školení pro prediktivní modelování úlohy, například klasifikační nebo regresní úlohy. Cílem je vybrat podmnožinu funkcí z původní datové sady, který snižte jeho dimenze pomocí minimální sadu funkcí, která představuje maximální množství odchylky v datech. Tuto podmnožinu funkcí se používá pro trénování modelu. Výběr funkce má dva hlavní účely.

* Nejprve výběr funkce často zvyšuje přesnost klasifikace odstraněním důležité, redundantní nebo vysoce korelují funkce.
* Druhý snižuje počet funkcí, který umožňuje zefektivnit proces školení modelu. Efektivita je obzvláště důležité pro inteligentních algoritmů, které jsou nákladné ke cvičení například support vector počítače.

I když výběr funkce Hledat a snížit počet funkcí v datové sadě použity při cvičení modelu, není výrazem "dimenzionalitu snížení" uvedené. Metody výběru funkce extrahujte podmnožinu původní funkcí v datech bez jejich změny.  Metody snížení dimenzionalitu využívají inženýrství funkce, které můžete transformace původní funkcí a proto je upravit. Příklady metod snížení dimenzionalitu: hlavní součást analýzy, kanonickém korelace analýzu a singulární rozložením. hodnota.

Mimo jiné jednu kategorii široce použité metody výběru funkcí v kontextu pod dohledem se nazývá "výběr filtru na základě funkce". Vyhodnocením korelace mezi jednotlivé funkce a atribut target použít tyto metody statistické míru přiřadit skóre pro každou funkci. Funkce, pak jsou seřazeny podle skóre, které mohou být použity ke nastavení prahové hodnoty pro zachování nebo odstranění konkrétní funkci. Příklady statistické míry použít v těchto metod: korelace, vzájemné informace a kvadratických test chí osoby.

V nástroji Azure Machine Learning Studio jsou modulů, které výběr funkce. Jak je znázorněno na následujícím obrázku, patří tyto moduly [na základě filtru výběr funkce] [ filter-based-feature-selection] a [Fisherovy lineární analýza Discriminant][fisher-linear-discriminant-analysis].

![Příklad výběru funkce](./media/select-features/feature-Selection.png)

Zvažte například použití [na základě filtru výběr funkce] [ filter-based-feature-selection] modulu. Pro větší pohodlí si pokračujte v použití příklad dolování textu. Předpokládejme, že budete chtít vytvořit regresní model po vytvoření sadu funkcí, 256 prostřednictvím [Hashování] [ feature-hashing] modulu a že je proměnná odpovědi "Sloupec1" obsahující kniha zkontrolujte hodnocení od 1 do 5. Nastavením "Funkci vyhodnocování metoda" na "Pearson korelace", "cílový sloupec" "Sloupec1" a "Počet požadované funkce" na 50. Potom modul [na základě filtru výběr funkce] [ filter-based-feature-selection] neprodukuje datovou sadu obsahující 50 funkcí společně s atribut target "Sloupec1". Následující obrázek znázorňuje tok tohoto experimentu a vstupní parametry:

![Příklad výběru funkce](./media/select-features/feature-Selection1.png)

Následující obrázek znázorňuje výsledné datové sady:

![Příklad výběru funkce](./media/select-features/feature-Selection2.png)

Jednotlivé funkce skóre pro magnitudu na základě na Pearson korelace mezi samostatně a atribut target "Sloupec1". Funkce s nejvyšší skóre jsou zachovány.

Na následujícím obrázku jsou zobrazeny odpovídající skóre vybraných funkcí:

![Příklad výběru funkce](./media/select-features/feature-Selection3.png)

Použitím to [na základě filtru výběr funkce] [ filter-based-feature-selection] modulu, 50 mimo 256 funkce jsou vybrané, protože mají nejvíce korelační funkce s Cílová proměnná "Sloupec1", založené na metodě vyhodnocování "Pearson korelace".

## <a name="conclusion"></a>Závěr
Funkce inženýrství a výběr funkce jsou dvě běžně analyzovány a vybrané funkce zvýšení efektivity školení procesu, který pokusy o extrahování klíčových informací obsažených v datech. Také zvyšují výkon těchto modelů přesně klasifikovat vstupních dat a k předvídání více robustní výsledky, které vás zajímají. Funkce inženýrství a výběr můžete také kombinovat aby výukové výpočetně tractable. Dělá to tak rozšíření a pak snižuje počet funkcí, které jsou potřeba k nakalibrovat nebo cvičení modelu. Funkce vybrané pro trénování modelu matematicky platí, že jsou minimální sadu nezávislých proměnných, které popisují vzorů v datech a pak úspěšně předpovědi výstupy.

Není vždy nutně provést výběr funkce inženýrství nebo funkce. Zda je nutné nebo ne závisí na data shromážděná, algoritmus vybrané a cílem experimentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

