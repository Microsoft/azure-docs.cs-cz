---
title: 'Vícetřídní rozhodovací doménová struktura: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Multiclass Decision Forest v Azure Machine Learning vytvořit model strojového učení založený na algoritmu *rozhodovací doménové struktury.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477540"
---
# <a name="multiclass-decision-forest-module"></a>Modul vícetřídní rozhodovací doménové struktury

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu strojového učení založeného na algoritmu *rozhodovací doménové struktury.* Rozhodovací doménová struktura je souborový model, který rychle vytváří řadu rozhodovacích stromů a zároveň se učí z označených dat.

## <a name="more-about-decision-forests"></a>Více o rozhodovacích lesích

Algoritmus rozhodovací doménové struktury je metoda učení souboru pro klasifikaci. Algoritmus funguje tak, že staví více rozhodovacích stromů a pak *hlasuje* o nejoblíbenější výstupní třídě. Hlasování je forma agregace, ve které každý strom v rozhodnutí o klasifikaci doménové struktury výstupy non-normalizované frekvence histogram popisků. Proces agregace sčítá tyto histogramy a normalizuje výsledek získat "pravděpodobnosti" pro každý popisek. Stromy, které mají vysokou predikci důvěru mají větší váhu v konečném rozhodnutí souboru.

Rozhodovací stromy obecně jsou neparametrické modely, což znamená, že podporují data s různými distribucemi. V každém stromu je spuštěna posloupnost jednoduchých testů pro každou třídu, čímž se zvýší úrovně stromové struktury, dokud není dosaženo uzlu (rozhodnutí).

Rozhodovací stromy mají mnoho výhod:

+ Mohou představovat nelineární hranice rozhodnutí.
+ Jsou efektivní při výpočtu a využití paměti během školení a predikce.
+ Provádějí integrovaný výběr a klasifikaci funkcí.
+ Jsou odolné v přítomnosti hlučných rysů.

Třídění doménové struktury rozhodnutí v Azure Machine Learning se skládá ze souboru stromů rozhodnutí. Obecně platí, že modely souborů poskytují lepší pokrytí a přesnost než stromy s jedním rozhodnutím. Další informace naleznete v tématu [Rozhodnutí stromy](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak nakonfigurovat doménovou strukturu rozhodnutí více tříd

1. Přidejte modul **Víceřádkové rozhodovací doménové struktury** do kanálu v návrháři. Tento modul najdete v části **Strojové učení**, **Inicializovat model**a **Klasifikace**.

2. Poklepáním na modul otevřete podokno **Vlastnosti.**

3. V **případě metody převzorkování**zvolte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z pytlování nebo replikace.

    + **Pytlování**: Pytlování se také nazývá *bootstrap agregace*. V této metodě je každý strom pěstován na nové vzorku, vytvořené náhodným vzorkováním původní datové sady s nahrazením, dokud nemáte datovou sadu velikost originálu. Výstupy modelů jsou kombinovány *hlasováním*, což je forma agregace. Další informace naleznete v položce Wikipedie pro agregaci Bootstrap.

    + **Replikovat**: Při replikaci je každý strom trénován na přesně stejná vstupní data. Určení, který rozdělený predikát se používá pro každý uzel stromu zůstává náhodné, vytváří různé stromy.

   

4. Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**

    + **Jeden parametr**: Tuto možnost vyberte, pokud víte, jak chcete model nakonfigurovat, a zadejte sadu hodnot jako argumenty.

    + **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.   

5. **Počet rozhodovacích stromů**: Zadejte maximální počet rozhodovacích stromů, které lze vytvořit v souboru. Vytvořením více rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale doba tréninku se může zvýšit.

    Tato hodnota také určuje počet stromů zobrazených ve výsledcích při vizualizaci trénovaného modelu. Chcete-li zobrazit nebo vytisknout jeden strom, můžete nastavit hodnotu na 1; to však znamená, že lze vytvořit pouze jeden strom (strom s počáteční sadou parametrů) a nejsou prováděny žádné další iterace.

6. **Maximální hloubka rozhodovacích stromů**: Zadejte číslo pro omezení maximální hloubky libovolného rozhodovacího stromu. Zvýšení hloubky stromu může zvýšit přesnost, s rizikem nadměrného vybavení a prodloužení doby tréninku.

7. **Počet náhodných rozdělení na uzel**: Zadejte počet rozdělení, které chcete použít při vytváření každého uzlu stromu. *Rozdělení* znamená, že prvky v každé úrovni stromu (uzlu) jsou náhodně rozděleny.

8. **Minimální počet vzorků na uzel listu**: Uveďte minimální počet případů, které jsou nutné k vytvoření libovolného koncového uzlu (listu) ve stromu. Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel.

    Například s výchozí hodnotou 1 může i jeden případ způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, data školení by musel obsahovat alespoň pět případů, které splňují stejné podmínky.



10. Připojte označenou datovou sadu a jeden z trénovacích modulů:

    + Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](./train-model.md)

11. Odešlete potrubí.



## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 