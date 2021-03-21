---
title: Jak vybrat algoritmus strojového učení
titleSuffix: Azure Machine Learning
description: Jak vybrat Azure Machine Learning algoritmy pro dohled nad clustery, klasifikací nebo regresní experimenty v režimu pod dohledem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 27b18fdc2dda40f8361483e6ecce28d0ccbd0310
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93308230"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Jak vybrat algoritmy pro Azure Machine Learning

Běžným dotazem je "který algoritmus strojového učení mám použít?" Vybraný algoritmus závisí hlavně na dvou různých aspektech vašeho scénáře pro datové vědy:

 - **Co chcete s daty dělat?** Konkrétně je to, co je to obchodní otázka, kterou chcete zodpovědět, pomocí učení z minulých dat?

 - **Jaké jsou požadavky vašeho scénáře pro datové vědy?** Konkrétně jaká je přesnost, čas školení, linearita, počet parametrů a počet funkcí, které vaše řešení podporuje?

 ![Důležité informace týkající se výběru algoritmů: co chcete vědět? Jaké jsou požadavky na scénář?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Obchodní scénáře a Tahákový list s algoritmem Machine Learning

[List tahák Algorithm](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri) vám pomůže s prvními aspekty: **co chcete s daty dělat**? Azure Machine Learning Na listu Machine Learning tahák Algorithm vyhledejte úkol, který chcete provést, a pak vyhledejte algoritmus [Azure Machine Learning Designer](./concept-designer.md?WT.mc_id=docs-article-lazzeri) pro řešení prediktivní analýzy. 

Machine Learning Designer poskytuje komplexní portfolio algoritmů, jako je například více [tříd rozhodovací doménové struktury](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri), [systémy doporučení](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri), [regrese sítě neuronové](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri), [neuronové síť s více třídami](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri)a [K – znamená clustering](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri). Každý algoritmus je navržený tak, aby vyřešil jiný typ problému strojového učení. Úplný seznam spolu s dokumentací o tom, jak každý algoritmus funguje a jak vyladit parametry pro optimalizaci algoritmu, najdete v části [algoritmus návrháře Machine Learning a příručka k modulům](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) .

> [!NOTE]
> Pokud si chcete stáhnout list tahák pro algoritmus strojového učení, jděte do [listu tahák pro algoritmus Azure Machine Learning](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).
> 
> 

Spolu s pokyny Azure Machine Learning v listu tahák Algorithm si pamatujte na jiné požadavky při výběru algoritmu strojového učení pro vaše řešení. Níže jsou uvedené další faktory, které je potřeba vzít v úvahu, jako je přesnost, doba školení, linearita, počet parametrů a počet funkcí.

## <a name="comparison-of-machine-learning-algorithms"></a>Porovnání algoritmů strojového učení

Některé výukové algoritmy vedou konkrétní předpoklady ke struktuře dat nebo k požadovaným výsledkům. Pokud zjistíte, které vyhovuje vašim potřebám, může vám dodávat užitečnější výsledky, přesnější předpovědi nebo rychlejší školicí časy.

Následující tabulka shrnuje některé nejdůležitější charakteristiky algoritmů ze skupin klasifikace, regrese a clusteringu:

| **Algoritmus** | **Údajů** | **Doba trénování** | **Linearita** | **Parametry** | **Poznámky** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Rodina klasifikace** | | | | | |
| [Logistická regrese dvou tříd](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Dobré  |Rychlý |Yes |4 | |
| [Rozhodovací doménová struktura se dvěma třídami](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Vynikající |Pokročilé |No |5 |Zobrazuje pomalejší časy vyhodnocování. Navrhněte, že nepracuje s 1-versus-all Multiclass z důvodu pomalejších časů bodování způsobených uzamykáním běhounů při hromadění předpovědi stromu. |
| [Zvýšení rozhodovacího stromu se dvěma třídami](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Vynikající |Pokročilé |No |6 |Velké nároky na paměť |
| [Neuronové síť se dvěma třídami](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |Dobré |Pokročilé |No |8 | |
| [Průměrná hodnota Perceptron se dvěma třídami](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |Dobré |Pokročilé |Yes |4 | |
| [Vektorový počítač podpory dvou tříd](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |Dobré |Rychlý |Yes |5 |Vhodné pro velké sady funkcí |
| [Mikrotřída logistické regrese](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Dobré |Rychlý |Yes |4 | |
| [Více tříd – rozhodovací doménová struktura](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Vynikající |Pokročilé |No |5 |Zobrazuje pomalejší časy vyhodnocování. |
| [Více tříd pro rozhodovací strom s více třídami](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Vynikající |Pokročilé |No |6 | Má za následek zlepšení přesnosti s malým rizikem méně pokrytí |
| [Neuronové síť pro více tříd](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |Dobré |Pokročilé |No |8 | |
| [Jedna a více tříd – All](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Zobrazí vlastnosti vybrané metody dvě třídy. |
| **Řada regrese** | | | | | |
| [lineární regrese](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |Dobré |Rychlý |Yes |4 | |
| [Regrese rozhodovací doménové struktury](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|Vynikající |Pokročilé |No |5 | |
| [Zvýšila se regrese rozhodovacího stromu.](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |Vynikající |Pokročilé |No |6 |Velké nároky na paměť |
| [Regrese sítě neuronové](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |Dobré |Pokročilé |No |8 | |
| **Řada clusteringu** | | | | | |
| [K-znamená clustering](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |Vynikající |Pokročilé |Yes |8 |Algoritmus clusteringu |

## <a name="requirements-for-a-data-science-scenario"></a>Požadavky na scénář pro datové vědy

Jakmile víte, co chcete s daty udělat, musíte určit další požadavky pro vaše řešení. 

Proveďte volby a případné kompromisy pro následující požadavky:

- Údajů
- Doba trénování
- Linearita
- Počet parametrů
- Počet funkcí

## <a name="accuracy"></a>Údajů

Přesnost v rámci strojového učení měří efektivitu modelu jako poměr skutečných výsledků do celkového počtu případů. V Návrháři Machine Learning [modul vyhodnocení modelu](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) vypočítá sadu standardních metrik vyhodnocení v oboru. Tento modul můžete použít k měření přesnosti trained model.

Získání nejpřesnější možné odpovědi není vždy nutné. V závislosti na tom, co chcete použít pro, je někdy sblížení vhodné. Pokud je to tento případ, může být možné zkrátit čas zpracování tím, že se dokončí více přibližnými metodami. Přibližné metody také přirozeně zabraňují přebudování.

Existují tři způsoby použití modulu vyhodnocení modelu:

- Vygenerujte skóre pro vaše školicí údaje za účelem vyhodnocení modelu.
- Generování skóre modelu, ale porovnání těchto skóre s skóre na rezervované sadě testů
- Porovnání skóre dvou různých, ale souvisejících modelů, pomocí stejné sady dat

Úplný seznam metrik a přístupů, pomocí kterých můžete vyhodnotit přesnost modelů strojového učení, najdete v tématu [vyhodnocení modulu modelu](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Doba trénování

V dohledovém učení znamená školení pomocí historických dat vytvořit model strojového učení, který minimalizuje chyby. Počet minut nebo hodin nutných ke vzdělávání modelu se v různých algoritmech liší. Čas školení je často úzce vázaný na přesnost. Druhá se většinou doprovází. 

Kromě toho jsou některé algoritmy citlivější na počet datových bodů než jiné. Můžete zvolit konkrétní algoritmus, protože máte časové omezení, zejména v případě, že je datová sada velká.

V Návrháři Machine Learning je vytváření a používání modelu Machine Learning obvykle proces tří kroků:

1.  Nakonfigurujte model výběrem konkrétního typu algoritmu a následným definováním jeho parametrů nebo parametrů. 

2.  Poskytněte datovou sadu, která je označena a má data kompatibilní s algoritmem. Připojte data i model k [modulu výuka modelu](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri).

3.  Po dokončení školení použijte školicí model s jedním z [modulů bodování](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri) a předpovědi se na nová data.

## <a name="linearity"></a>Linearita

Linearita v statistice a strojovém učení znamená, že mezi proměnnou a konstantou v datové sadě existuje lineární vztah. Například algoritmy lineární klasifikace předpokládají, že třídy mohou být odděleny rovnou čárou (nebo jeho vyšším rozlišením analogového).

Spousta algoritmů strojového učení využívá linearitu. V Návrháři Azure Machine Learning jsou: 

- [Mikrotřída logistické regrese](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Logistická regrese dvou tříd](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Podpora vektorových počítačů](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

Algoritmy lineární regrese předpokládají, že trendy dat následují rovnou čáru. Tento předpoklad není pro některé problémy správný, ale u ostatních omezuje přesnost. Bez ohledu na jejich nevýhody se jako první strategii populární lineární algoritmy. Mají za následek algorithmically jednoduché a rychlé ke školení.

![Nelineární hranice třídy](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Nelineární hranice třídy** _: _Relying v algoritmu lineární klasifikace by výsledkem byla nízká přesnost. *

![Data s nelineárním trendem](./media/how-to-select-algorithms/nonlinear-trend.png)

***Data s nelineárním trendem** _: _Using metoda lineární regrese by vygenerovala mnohem větší chybu, než je potřeba. *

## <a name="number-of-parameters"></a>Počet parametrů

Parametry jsou ovladače, které vám pomohou při nastavování algoritmu zapnout datový vědecký pracovník. Jsou to čísla, která mají vliv na chování algoritmu, jako je odolnost proti chybám, počet iterací nebo možnosti mezi variantami, jak se algoritmus chová. Čas školení a přesnost algoritmu je někdy citlivý, aby bylo možné získat pouze ta správná nastavení. Algoritmy s velkým počtem parametrů obvykle vyžadují většinu zkušební verze a chybu, aby bylo možné najít dobrou kombinaci.

Alternativně je v Návrháři Machine Learning [modul předparametrů modelu ladění](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) : cílem tohoto modulu je určení optimálních parametrů pro model strojového učení. Modul vytváří a testuje více modelů pomocí různých kombinací nastavení. Porovnává metriky pro všechny modely a získá kombinace nastavení. 

I když je to skvělý způsob, jak se ujistit, že jste přepnuli prostor parametrů, čas potřebný ke zvýšení modelu se zvyšuje exponenciálně s počtem parametrů. Je to, že má mnoho parametrů obvykle indikuje, že algoritmus má větší flexibilitu. Často dosahuje velmi dobré přesnosti, pokud můžete najít správnou kombinaci nastavení parametrů.

## <a name="number-of-features"></a>Počet funkcí

Ve službě Machine Learning je funkce kvantifikovaná proměnná pro jev, který se snažíte analyzovat. U určitých typů dat může být počet funkcí velmi velký v porovnání s počtem datových bodů. To je často případ s geneticky nebo textovými daty. 

Velký počet funkcí může zpomalitovat některé algoritmy učení, takže školení se unfeasibly dlouhou dobu. [Podpora vektorových počítačů](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) je zvláště vhodná pro scénáře s vysokým počtem funkcí. Z tohoto důvodu byly použity v mnoha aplikacích z načítání informací do klasifikace textu a obrázku. Podporu vektorových počítačů lze použít pro úlohy klasifikace i regrese.

Výběr funkcí odkazuje na proces použití statistických testů na vstupy v zadaném výstupu. Cílem je určit, které sloupce mají více prediktivní výstup. [Modul výběru funkce založený na filtrech](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) v Návrháři Machine Learning poskytuje několik algoritmů výběru funkcí, ze kterých si můžete vybrat. Modul zahrnuje metody korelace, jako je korelace Pearsonova a hodnoty chí-kvadrát.

Můžete také použít [modul důležitost funkcí permutace](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) k výpočtu sady výsledků důležitosti funkce pro datovou sadu. Pak můžete využít tyto výsledky, abyste mohli určit nejlepší funkce pro použití v modelu.

## <a name="next-steps"></a>Další kroky

 - [Další informace o Azure Machine Learning designeru](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - Popisy všech algoritmů strojového učení, které jsou dostupné v Azure Machine Learning designeru, najdete v tématu [algoritmus Machine Learning designeru a Reference k modulům](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) .
 - Informace o tom, jak prozkoumat vztah mezi hlubokou výukou, Machine Learningem a AI, najdete v článku o [podrobném učení vs. Machine Learning](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)