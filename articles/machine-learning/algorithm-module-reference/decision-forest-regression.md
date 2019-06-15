---
title: 'Rozhodovací les regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu regrese rozhodovacího doménové struktury ve službě Azure Machine Learning k vytvoření regresní model založený na kompletu stromů rozhodnutí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65442358"
---
# <a name="decision-forest-regression-module"></a>Rozhodovací les regrese modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření regresní model založený na kompletu stromů rozhodnutí.

Po nakonfigurování modelu musí trénování modelu s použitím datové sady s popiskem a [trénování modelu](./train-model.md) modulu.  Trénovaného modelu je pak možné k následné predikci. 

## <a name="how-it-works"></a>Jak to funguje

Rozhodovací stromy jsou – čištění modely, které provádějí posloupnost jednoduchých testů pro každou instanci struktury dat binárního stromu procházení, dokud nebude dosaženo uzel typu list (rozhodnutí).

Rozhodovací stromy mají tyto výhody:

- Jsou efektivní výpočet a využití paměti při trénování a predikcí.

- Mohou představovat hranice nelineárních rozhodnutí.

- Tyto provést výběr integrované funkce a klasifikace a jsou odolné za přítomnosti hlučného funkce.

Tento regresní model se skládá z kompletu stromů rozhodnutí. Každý stromu v doménové struktuře regrese rozhodovacího výstupy Gaussovské distribuce jako predikcí. Agregace se provádí přes skupiny stromů stromů najít nejbližší kombinované rozdělení pro všechny stromy Gaussovské distribuce v modelu.

Další informace o teoretické rámec pro tento algoritmus a jeho implementaci najdete v článku: [Doménové struktury rozhodnutí: Jednotné rozhraní pro klasifikace, regrese, hustota odhad potrubí učení a částečně učení](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak nakonfigurovat rozhodnutí doménové struktury regresní Model

1. Přidat **rozhodnutí doménové struktury regrese** modulů na experiment. Modul můžete najít v rozhraní v rámci **Machine Learning**, **inicializovat Model**, a **regrese**.

2. Otevřete vlastnosti modulu a pro **převzorkování metoda**, zvolte metodu použitou k vytvoření jednotlivé stromové struktury.  Můžete si vybrat z **pytlování** nebo **replikovat**.

    - **Pytlování**: Pytlování se také nazývá *bootstrap agregaci*. Každý stromu v doménové struktuře regrese rozhodovacího výstupy Gaussovské distribuce prostřednictvím předpovědi. Agregace je vyhledání Gaussovské, jehož první dva okamžiků odpovídat okamžiků kombinaci Gaussians zadaný kombinací všech Gaussians vrácený jednotlivé stromové struktury.

         Další informace najdete v tématu zadání Wikipedia [Bootstrap agregaci](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikovat**: Při replikaci každý stromu trénovaných na přesně stejné vstupní data. Určení, které rozdělení predikát použije se pro každý uzel stromu zůstane náhodné a stromy budou mít různé.

         Další informace o procesu trénování s **replikovat** možnosti najdete v tématu [rozhodnutí doménové struktury pro počítačové zpracování obrazu a lékařské analýzu obrázku. Criminisi a J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.

    - **Jeden parametr**

      Pokud víte, jak chcete provést konfiguraci modelu, můžete zadat konkrétní sadu hodnot jako argumenty. Může se naučili tyto hodnoty pomocí služby experimentování ve službě nebo přijata jako doprovodné materiály.



4. Pro **počet rozhodovacích stromů**, určení celkového počtu rozhodovacích stromů k vytvoření v skupiny stromů. Vytvořením další rozhodovacích stromů, můžete potenciálně získat lepší pokrytí, ale čas školení zvýší.

    > [!TIP]
    > Tato hodnota také řídí počet stromové struktury zobrazují při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jediného stromu, můžete nastavit hodnotu 1. to ale znamená jenom jeden stromu bude vytvořen (stromové struktury s počáteční sadu parametrů) a budou provedeny žádné další iterace.

5. Pro **maximální hloubka rozhodovacích stromů**, zadejte číslo pro omezení maximální hloubky jakékoli rozhodovací strom. Zvýšení hloubku stromu může zvýšit přesnost, nese chvíli školení overfitting a vyšší.

6. Pro **počet náhodných rozdělí na jeden uzel**, zadejte počet rozdělení pro použití při vytváření jednotlivých uzlů stromu. A *rozdělit* náhodně dělí prostředky, které nabízí v každé úrovni stromu (uzel).

7. Pro **minimální počet vzorků za uzel typu list**, určit minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (listový) ve stromové struktuře.

     Zvýšením tuto hodnotu zvýšit prahovou hodnotu pro vytvoření nového pravidla. Výchozí hodnotu 1, ještě jeden případ může způsobit, který se má vytvořit nové pravidlo. Pokud zvýšíte hodnotu 5, museli byste trénovací data obsahují aspoň pět případy, které splňují stejných podmínek.


9. Připojení s popiskem datovou sadu, vyberte přípony sloupec, který obsahuje více než dva výsledky a připojte se k [Train Model](./train-model.md).

    - Pokud nastavíte **režimu vytváření trainer** umožňuje **jediný parametr**, trénování modelu s použitím [trénování modelu](./train-model.md) modulu.

   

10. Spusťte experiment.

### <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete zobrazit stromu, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem na výstup modulu školení a vyberte **vizualizovat**.

+ Pokud chcete zobrazit pravidla pro každý uzel, klikněte na každém stromu a podrobnostem rozdělení.

+ Chcete-li uložit snímek trénovaného modelu, klikněte pravým tlačítkem na výstup modulu školení a vyberte **uložit jako Trénovaného modelu**. Tuto kopii modelu není aktualizován na následné spuštění experimentu. 

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 