---
title: Výběr funkcí v rámci vědeckého zpracování týmových dat
description: Vysvětluje účel výběru funkcí a poskytuje příklady jejich role v procesu rozšíření dat Machine Learning.
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
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321324"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Výběr funkcí ve vědeckém zpracování týmových dat (TDSP)
Tento článek vysvětluje účely výběru funkcí a poskytuje příklady jeho role v procesu rozšíření dat Machine Learning. Tyto příklady se vykreslí z Azure Machine Learning Studio.

Inženýrské a výběr funkcí je jednou z vědeckých procesů týmových dat (TDSP), které jsou uvedené v článku [co je vědecký proces týmových dat?](overview.md). Technologie a výběr funkcí jsou části kroku **vývoj funkcí** TDSP.

* **inženýrské funkce**: Tento proces se pokusí vytvořit další relevantní funkce z existujících nezpracovaných funkcí v datech a zvýšit prediktivní sílu na školicí algoritmus.
* **Výběr funkcí**: Tento proces vybere podmnožinu klíčových prvků pro původní data v pokusu snížit dimenzionální problém při výuce.

Obvykle se pro generování dalších funkcí používá **inženýr funkcí** a pak se provede krok **výběru funkcí** , který eliminuje nepodstatné, redundantní nebo vysoce korelační funkce.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrování funkcí z výběru funkcí pro data
Výběr funkcí lze použít pro úlohy klasifikace nebo regrese. Cílem je vybrat podmnožinu funkcí z původní datové sady, která zmenší své rozměry pomocí minimální sady funkcí k vyjádření maximálního množství odchylek v datech. Tato podmnožina funkcí slouží ke výukě modelu. Výběr funkcí slouží dvěma hlavním účelům.

* Za prvé, výběr funkcí často zvyšuje přesnost klasifikace odstraněním nepodstatných, redundantních nebo vysoce korelačních funkcí.
* Za druhé sníží počet funkcí, což vede k efektivnějšímu procesu školicích modelů. Efektivita je důležitá pro informace, které jsou nákladné pro výuku, jako je například podpora vektorových počítačů.

I když výběr funkcí se snaží snížit počet funkcí v datové sadě, která se používá ke výukě modelu, není odkazována výrazem "snižování dimenzionálního". Metody výběru funkcí extrahují podmnožinu původních funkcí v datech beze změny.  Metody snižování dimenzionálního využití využívají inženýrské funkce, které mohou transformovat původní funkce a tak je upravovat. Příklady metod snížení hodnoty dimenzionální zahrnují analýzu hlavních komponent, kanonickou analýzu korelace a rozložení hodnot v čísle.

Mimo jiné se jedna široce používaná kategorie metod výběru funkcí v kontextu pod dohledem nazývá výběr funkce založený na filtrech. Vyhodnocením korelace mezi jednotlivými funkcemi a cílovým atributem tyto metody použijí statistickou míru pro přiřazení skóre k jednotlivým funkcím. Funkce jsou pak seřazené podle skóre, které se dá použít k nastavení prahové hodnoty pro zachování nebo odstranění konkrétní funkce. Příklady statistických měření používaných v těchto metodách zahrnují korelaci osob, vzájemné informace a druhý test chí.

V Azure Machine Learning Studio jsou k dispozici moduly pro výběr funkcí. Jak je znázorněno na následujícím obrázku, tyto moduly zahrnují [Výběr funkcí založených na filtrech][filter-based-feature-selection] a [Fisher lineární analýzu Discriminant][fisher-linear-discriminant-analysis].

![Moduly výběru funkcí](./media/select-features/feature-Selection.png)

Vezměte v úvahu například použití modulu [výběru funkcí založených na filtrech][filter-based-feature-selection] . Pro usnadnění práce pokračujte v příkladu dolování textu. Předpokládejme, že chcete sestavit regresní model poté, co je vytvořena sada funkcí 256 pomocí modulu [hashování funkcí][feature-hashing] a že proměnná odpovědi je "Sloupec1", která obsahuje knihu hodnocení v rozsahu od 1 do 5. Nastavením "metoda bodování funkce" na "Pearsonova Correlation", "cílový sloupec", který bude "Sloupec1", a "počet požadovaných funkcí" na 50. Pak [Výběr funkce založený na filtrech][filter-based-feature-selection] vytvoří datovou sadu obsahující 50 funkcí společně s atributem target "Sloupec1". Následující obrázek ukazuje tok tohoto experimentu a vstupní parametry:

![Filter-Based vlastností modulu výběru funkcí](./media/select-features/feature-Selection1.png)

Následující obrázek ukazuje výsledné datové sady:

![Výsledná datová sada pro modul výběru funkcí založených na filtrech](./media/select-features/feature-Selection2.png)

Každá funkce je vyhodnocena na základě korelace Pearsonova mezi sebou a cílovým atributem "Sloupec1". Funkce s nejvyšším skóre jsou zachovány.

Odpovídající skóre vybraných funkcí je znázorněno na následujícím obrázku:

![Skóre pro modul výběru funkcí založených na filtrech](./media/select-features/feature-Selection3.png)

Použitím tohoto modulu [výběru funkcí založeného na filtrech][filter-based-feature-selection] se vybere 50 z 256 funkcí, protože mají nejvíc korelující funkce s cílovou proměnnou "Sloupec1" na základě metody bodování "Pearsonova Correlation".

## <a name="conclusion"></a>Závěr
Vytváření funkcí a výběr funkcí jsou dvě běžně navržené a vybrané funkce zvyšují efektivitu školicího procesu, který se pokouší extrahovat informace o klíčích obsažených v datech. Také zlepšují sílu těchto modelů, aby přesně klasifikaci vstupních dat a předpovědi výsledků jejich zájmu byly robustnější. Vytváření a výběr funkcí se také můžou kombinovat, aby se učení lépe vyjímat. Tím provedete vylepšením a následným snížením počtu funkcí potřebných k kalibraci nebo výukovém modelu. Matematicky řečeno, funkce vybrané pro výuku modelu jsou minimální sada nezávislých proměnných, které vysvětlují vzory v datech a pak mají úspěšné předpověď výsledků.

Nemusíte vždycky nutně provádět strojírenství funkcí nebo výběr funkcí. Bez ohledu na to, jestli je to potřeba, závisí na shromažďovaných datech, zvoleném algoritmu a cíli experimentu.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis