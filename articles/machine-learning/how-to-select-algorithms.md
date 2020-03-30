---
title: Jak vybrat algoritmus strojového učení
titleSuffix: Azure Machine Learning
description: Jak vybrat algoritmy Azure Machine Learning pro učení pod dohledem a bez dohledu v clustering, klasifikace nebo regresní experimenty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328659"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Jak vybrat algoritmy pro Azure Machine Learning

Častá otázka je "Jaký algoritmus strojového učení mám použít?" Algoritmus, který vyberete, závisí především na dvou různých aspektech vašeho scénáře datové vědy:

 - **Co chcete dělat se svými daty?** Konkrétně, jaká je obchodní otázka, na kterou chcete odpovědět tím, že se poučíte z minulých dat?

 - **Jaké jsou požadavky vašeho scénáře datové vědy?** Konkrétně, jaká je přesnost, doba tréninku, linearita, počet parametrů a počet funkcí, které vaše řešení podporuje?

 ![Důležité informace o výběru algoritmů: Co chcete vědět? Jaké jsou požadavky na scénář?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Obchodní scénáře a algoritmus strojového učení Cheat Sheet

[Cheat sheet algoritmu s algoritmem strojového učení Azure](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) vám pomůže s prvním aspektem: Co chcete dělat s **daty?** Na cheat listu algoritmu strojového učení vyhledejte úlohu, kterou chcete provést, a pak najděte algoritmus [návrháře Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) pro řešení prediktivní analýzy. 

Návrhář strojového učení poskytuje komplexní portfolio algoritmů, jako je [například víceřádková rozhodovací doménová struktura](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [systémy doporučení](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [regrese neuronové sítě](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [vícetřídní neuronová síť](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)a [clustering K-means](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Každý algoritmus je navržen tak, aby řešil jiný typ problému strojového učení. Podívejte se na [algoritmus machine learning designer a odkaz na modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) pro úplný seznam spolu s dokumentací o tom, jak každý algoritmus funguje a jak optimalizovat parametry pro optimalizaci algoritmu.

> [!NOTE]
> Chcete-li stáhnout algoritmus strojového učení tahák, přejděte na [Azure Machine learning algoritmus tahák](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Spolu s pokyny v Azure Machine Learning Algorithm Cheat Sheet, mějte na paměti další požadavky při výběru algoritmu strojového učení pro vaše řešení. Níže jsou uvedeny další faktory, které je třeba zvážit, jako je přesnost, doba tréninku, linearita, počet parametrů a počet funkcí.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Další požadavky na scénář datové vědy

Jakmile budete vědět, co chcete dělat s daty, musíte určit další požadavky pro vaše řešení. 

Vyhodnoťte se a případně kompromisy pro následující požadavky:

- Přesnost
- Doba trénování
- Linearita
- Počet parametrů
- Počet funkcí

## <a name="accuracy"></a>Přesnost

Přesnost ve strojovém učení měří účinnost modelu jako podíl skutečných výsledků na celkových případech. V návrháři Machine Learning [modul Evaluate Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) vypočítá sadu standardních metrik hodnocení. Tento modul můžete použít k měření přesnosti trénovaného modelu.

Získání co nejpřesnější odpovědi není vždy nutné. Někdy je aproximace přiměřená, v závislosti na tom, k čemu ji chcete použít. Pokud tomu tak je, můžete být schopni výrazně zkrátit dobu zpracování tím, že se budete držet více přibližných metod. Přibližné metody také přirozeně mají tendenci vyhnout se nadměrnému vybavení.

Modul Vyhodnotit model lze použít třemi způsoby:

- Generovat skóre přes vaše trénovací data za účelem vyhodnocení modelu
- Generovat skóre na modelu, ale porovnat tyto skóre skóre na vyhrazené testovací sady
- Porovnání skóre pro dva různé, ale související modely pomocí stejné sady dat

Úplný seznam metrik a přístupů, které můžete použít k vyhodnocení přesnosti modelů strojového učení, naleznete v [tématu Vyhodnocení modulu Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Doba trénování

V učení pod dohledem znamená školení použití historických dat k vytvoření modelu strojového učení, který minimalizuje chyby. Počet minut nebo hodin potřebných k trénování modelu se mezi algoritmy značně liší. Doba tréninku je často úzce spjata s přesností; jeden obvykle doprovází druhého. 

Kromě toho některé algoritmy jsou citlivější na počet datových bodů než ostatní. Můžete zvolit konkrétní algoritmus, protože máte časové omezení, zejména pokud je sada dat velká.

V návrháři Machine Learning je vytváření a používání modelu strojového učení obvykle třístupňový proces:

1.  Nakonfigurujte model výběrem konkrétního typu algoritmu a definováním jeho parametrů nebo hyperparametrů. 

2.  Zadejte datovou sadu, která je označena a má data kompatibilní s algoritmem. Připojte data i model k [modulu Train Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Po dokončení trénování použijte trénovaný model s jedním z [modulů hodnocení](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) k předpovědi na nová data.

## <a name="linearity"></a>Linearita

Linearita ve statistikách a strojovém učení znamená, že v datové sadě existuje lineární vztah mezi proměnnou a konstantou. Například lineární klasifikační algoritmy předpokládají, že třídy mohou být odděleny přímkou (nebo jejím vyšším rozměrovým analogem).

Mnoho algoritmů strojového učení využívá linearitu. V návrháři Azure Machine Learning zahrnují: 

- [Vícetřídní logistická regrese](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Dvoustupňová logistická regrese](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Podpůrné vektorové stroje](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Lineární regresní algoritmy předpokládají, že trendy dat se řídí přímkou. Tento předpoklad není špatný pro některé problémy, ale pro jiné snižuje přesnost. Navzdory jejich nevýhodám jsou lineární algoritmy populární jako první strategie. Mají tendenci být algoritmicky jednoduché a rychlé trénovat.

![Hranice nelineární třídy](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Hranice nelineární třídy***: *Spoléhání se na algoritmus lineární klasifikace by mělo za následek nízkou přesnost.*

![Data s nelineárním trendem](./media/how-to-select-algorithms/nonlinear-trend.png)

***Data s nelineárním trendem***: *Použití metody lineární regrese by generovalo mnohem větší chyby, než bylo nutné.*

## <a name="number-of-parameters"></a>Počet parametrů

Parametry jsou knoflíky, které datový vědec zapne při nastavování algoritmu. Jsou to čísla, která ovlivňují chování algoritmu, jako je například tolerance chyb nebo počet iterací nebo možnosti mezi variantami chování algoritmu. Doba školení a přesnost algoritmu může být někdy citlivé na získání správné nastavení. Algoritmy s velkým počtem parametrů obvykle vyžadují nejvíce pokusů a omylů najít dobrou kombinaci.

Alternativně je [tune model hyperparameters modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) v návrháři strojového učení: Cílem tohoto modulu je určit optimální hyperparameters pro model strojového učení. Modul vytvoří a testuje více modelů pomocí různých kombinací nastavení. Porovnává metriky ve všech modelech, aby získal kombinace nastavení. 

I když je to skvělý způsob, jak se ujistit, že jste rozloženy prostor parametru, čas potřebný k trénování modelu zvyšuje exponenciálně s počtem parametrů. Výhodou je, že s mnoha parametry obvykle označuje, že algoritmus má větší flexibilitu. To může často dosáhnout velmi dobré přesnosti, za předpokladu, že najdete správnou kombinaci nastavení parametrů.

## <a name="number-of-features"></a>Počet funkcí

Ve strojovém učení je funkce kvantifikovatelnou proměnnou jevu, který se pokoušíte analyzovat. U určitých typů dat může být počet funkcí velmi velký ve srovnání s počtem datových bodů. To je často případ genetiky nebo textových dat. 

Velké množství funkcí může zabořit některé algoritmy učení, takže doba školení je neproveditelná. [Podpůrné vektorové počítače](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) jsou obzvláště vhodné pro scénáře s vysokým počtem funkcí. Z tohoto důvodu byly použity v mnoha aplikacích od načítání informací až po klasifikaci textu a obrázků. Podpůrné vektorové počítače lze použít pro úlohy klasifikace i regrese.

Výběr funkce odkazuje na proces aplikace statistických testů na vstupy, vzhledem k zadanému výstupu. Cílem je určit, které sloupce jsou prediktivnější výstupu. [Modul výběru funkcí založených na filtru](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) v návrháři strojového učení poskytuje více algoritmů výběru funkcí, ze kterých si můžete vybrat. Modul obsahuje korelační metody, jako je například Pearsonova korelace a hodnoty chí-kvadrát.

Modul [Důležitost funkce permutace](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) můžete také použít k výpočtu sady skóre důležitosti funkce pro vaši datovou sadu. Potom můžete využít tyto skóre, které vám pomohou určit nejlepší funkce pro použití v modelu.


## <a name="next-steps"></a>Další kroky

 - [Další informace o návrháři Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Popis všech algoritmů strojového učení dostupných v návrháři Azure Machine Learning najdete v tématu [Algoritmus návrháře strojového učení a odkaz na moduly.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Chcete-li prozkoumat vztah mezi hlubokým učením, strojovým učením a umělou id. [Deep Learning vs. Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
