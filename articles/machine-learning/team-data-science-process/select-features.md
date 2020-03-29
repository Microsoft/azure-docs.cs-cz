---
title: Výběr funkcí v procesu vědecké ho svědecké ho spoje
description: Vysvětluje účel výběru funkcí a poskytuje příklady jejich role v procesu zlepšování dat strojového učení.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716685"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Výběr funkcí ve vědeckém zpracování týmových dat (TDSP)
Tento článek vysvětluje účely výběru funkcí a poskytuje příklady jeho role v procesu zlepšování dat strojového učení. Tyto příklady jsou vykresleny z Azure Machine Learning Studio.

Inženýring a výběr funkcí je součástí procesu vědecké vědy o týmových datech (TDSP) popsaného v článku [Co je proces vědecké vědy o týmových datech?](overview.md). Technické znalosti a výběr funkcí jsou součástí kroku **Vyvinout prvky** tdsp.

* **inženýring funkcí**: Tento proces se pokouší vytvořit další relevantní funkce ze stávajících nezpracovaných funkcí v datech a zvýšit prediktivní výkon algoritmu učení.
* **Výběr funkce**: Tento proces vybere klíčovou podmnožinu původních datových funkcí ve snaze snížit dimenzionalitu trénovacího problému.

Za normálních okolností **funkce inženýrství** se použije nejprve generovat další funkce a pak se provede krok **výběru funkce** k odstranění irelevantní, redundantní nebo vysoce korelované funkce.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrování funkcí z dat – výběr funkcí
Výběr funkcí lze použít pro klasifikaci nebo regresní úkoly. Cílem je vybrat podmnožinu prvků z původní datové sady, které snižují její rozměry pomocí minimální sady prvků představujících maximální míru rozptylu v datech. Tato podmnožina funkcí se používá k trénování modelu. Výběr funkcí slouží dvěma hlavním účelům.

* Za prvé, výběr funkcí často zvyšuje přesnost klasifikace odstraněním irelevantních, nadbytečných nebo vysoce korelovaných funkcí.
* Za druhé snižuje počet funkcí, což zefektivňuje proces školení modelu. Efektivita je důležitá pro studenty, kteří jsou nákladní na školení, jako jsou podpůrné vektorové stroje.

Přestože výběr funkcí se snaží snížit počet funkcí v datové sadě použité k trénování modelu, není označován termínem "snížení dimenzionality". Metody výběru prvků extrahují podmnožinu původních prvků v datech, aniž by je měnily.  Metody redukce dimenzionality využívají konstrukční prvky, které mohou transformovat původní prvky a tím je upravovat. Příklady metod redukce dimenzionality zahrnují analýzu hlavní součásti, kanonickou korelační analýzu a rozklad singulární hodnoty.

Mimo jiné jedna široce aplikovaná kategorie metod výběru funkcí v kontrolovaném kontextu se nazývá "výběr funkcí založený na filtru". Vyhodnocením korelace mezi jednotlivými funkcemi a cílovým atributem tyto metody používají statistické měřítko pro přiřazení skóre každé funkci. Funkce jsou pak seřazeny podle skóre, které mohou být použity k nastavení prahové hodnoty pro zachování nebo odstranění určité funkce. Příklady statistických opatření použitých v těchto metodách zahrnují korelaci osob, vzájemné informace a test na druhou chi.

V Azure Machine Learning Studio jsou moduly k dispozici pro výběr funkcí. Jak je znázorněno na následujícím obrázku, tyto moduly zahrnují [výběr funkcí založených na filtru][filter-based-feature-selection] a [fisherovu lineární diskriminanční analýzu][fisher-linear-discriminant-analysis].

![Moduly pro výběr funkcí](./media/select-features/feature-Selection.png)

Zvažte například použití modulu [výběru funkcí založených na filtru.][filter-based-feature-selection] Pro větší pohodlí pokračujte v používání příkladu dolování textu. Předpokládejme, že chcete vytvořit regresní model po sadu 256 funkcí jsou vytvořeny prostřednictvím modulu [hodnotit funkce][feature-hashing] a že proměnná odezvy je "Col1", který obsahuje hodnocení recenze knihy v rozmezí od 1 do 5. Nastavením "Metoda bodování funkce" se "Pearson korelace", "Cílový sloupec" se "Col1" a "Počet požadovaných funkcí" na 50. Potom modul [Filter-Based Feature Selection][filter-based-feature-selection] vytvoří datovou sadu obsahující 50 funkcí spolu s cílovým atributem "Col1". Následující obrázek znázorňuje tok tohoto experimentu a vstupní parametry:

![Vlastnosti modulu výběru funkcí založených na filtru](./media/select-features/feature-Selection1.png)

Následující obrázek znázorňuje výsledné datové sady:

![Výsledná datová sada pro modul výběru funkcí založených na filtru](./media/select-features/feature-Selection2.png)

Každá funkce je hodnocena na základě Pearsonovy korelace mezi sebou a cílovým atributem "Col1". Funkce s nejvyšším skóre jsou zachovány.

Odpovídající skóre vybraných prvků jsou zobrazena na následujícím obrázku:

![Skóre pro modul výběru funkcí založených na filtru](./media/select-features/feature-Selection3.png)

Použitím tohoto [modulu výběru funkcí založených na filtru][filter-based-feature-selection] je vybráno 50 z 256 funkcí, protože mají nejvíce korelované funkce s cílovou proměnnou "Col1", založenou na metodě hodnocení "Pearsonova korelace".

## <a name="conclusion"></a>Závěr
Technické funkce a výběr funkcí jsou dvě běžně inženýrské a vybrané funkce zvyšují efektivitu procesu školení, který se pokouší extrahovat klíčové informace obsažené v datech. Zlepšují také výkon těchto modelů pro přesné klasifikaci vstupních dat a robustnější předvídání výsledků zájmu. Funkce inženýrství a výběr může také kombinovat, aby se učení více výpočetní zvladatelné. Činí tak tím, že zvyšuje a pak snižuje počet funkcí potřebných ke kalibraci nebo trénování modelu. Matematicky řečeno, funkce vybrané pro trénování modelu jsou minimální sadu nezávislých proměnných, které vysvětlují vzory v datech a pak úspěšně předpovědět výsledky.

Není vždy nutné provádět technické nebo funkce. Zda je to potřeba nebo ne, závisí na shromážděných datech, vybraném algoritmu a cíli experimentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

