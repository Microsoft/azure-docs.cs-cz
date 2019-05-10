---
title: 'Rozhodovací les se multiclass: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Multiclass rozhodovací les se ve službě Azure Machine Learning vytvořit na základě model strojového učení *rozhodnutí doménové struktury* algoritmus.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 10364d014431a500e7c38a02d47f432cd464feb6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411449"
---
# <a name="multiclass-decision-forest-module"></a>Rozhodovací les se multiclass modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření model na základě strojového učení *rozhodnutí doménové struktury* algoritmus. Rozhodovací LES je komplet model, který rychle vytváří řadu rozhodovací stromy, při učení z dat označené.

## <a name="more-about-decision-forests"></a>Další informace o rozhodnutí doménových struktur

Algoritmus rozhodnutí doménové struktury je kompletu učení metodu klasifikace. Algoritmus funguje tak, že vytváření více rozhodovacích stromů a potom *hlasování* na nejoblíbenější třída output. Hlasování je způsob agregace, ve které každý stromu rozhodnutí o zařazení doménové struktury výstupy histogram nenormalizovaný četnost popisků. Proces agregace součet těchto histogramy a normalizuje výsledek, který má získat "pravděpodobností" pro každý popisek. Stromové struktury, které mají vysokou predikce mají větší váhu v konečné rozhodnutí skupiny stromů.

Rozhodovací stromy jsou obecně – čištění modely, což znamená, že podporují data pomocí různých distribucí. V každém stromu spuštění pořadí jednoduché testů pro každou třídu, zvýšení úrovně stromové struktury, dokud nebude dosaženo uzel typu list (rozhodnutí).

Rozhodovací stromy mají celou řadu výhod:

+ Mohou představovat hranice nelineárních rozhodnutí.
+ Jsou efektivní v výpočetní výkon a využití paměti při trénování a predikcí.
+ Provádějí výběr integrované funkce a klasifikace.
+ Jsou odolné za přítomnosti hlučného funkce.

Třídění rozhodnutí doménové struktury ve službě Azure Machine Learning se skládá z kompletu stromů rozhodnutí. Modely komplet obecně poskytují lepší pokrytí a přesnost než jeden rozhodovacích stromů. Další informace najdete v tématu [Decision trees](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak nakonfigurovat Multiclass rozhodovací les



1. Přidat **Multiclass rozhodovací les** modulu do experimentu v rozhraní. Můžete najít tento modul v rámci **Machine Learning**, **inicializovat Model**, a **klasifikace**.

2. Klikněte dvakrát na modul a otevřete **vlastnosti** podokně.

3. Pro **převzorkování metoda**, zvolte metodu použitou k vytvoření jednotlivé stromové struktury.  Můžete vybrat z pytlování nebo replikace.

    + **Pytlování**: Pytlování se také nazývá *bootstrap agregaci*. V této metodě se na nové ukázky, vytvořil náhodně vzorkování původní datové sady s nahrazení, dokud máte datovou sadu velikosti původní pěstuje každém stromu. Výstupy modelů jsou zkombinované podle *hlasování*, což je způsob agregace. Další informace naleznete v příspěvku Wikipedia pro zavedení agregaci.

    + **Replikovat**: Při replikaci každý stromu trénovaných na přesně stejné vstupní data. Určení, které rozdělení predikát použije se pro každý uzel stromu zůstane náhodné, vytváření různých stromů.

   

4. Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.

    + **Jeden parametr**: Tuto možnost vyberte, pokud víte, jak chcete konfiguraci modelu a poskytují sadu hodnot jako argumenty.


5. **Počet rozhodovacích stromů**: Zadejte maximální počet rozhodovacích stromů, které mohou být vytvořeny v skupiny stromů. Vytvořením další rozhodovacích stromů potenciálně získáte lepší pokrytí, ale může zvýšit dobu výuky.

    Tato hodnota také řídí počet stromů zobrazí ve výsledcích při vizualizaci trénovaného modelu. Zobrazit nebo vytisknout jediného stromu, můžete nastavit hodnotu 1. to ale znamená jenom jeden stromové struktuře může být vytvořen (stromové struktury s počáteční sadu parametrů), a jsou prováděny žádné další iterace.

6. **Maximální hloubka rozhodovacích stromů**: Zadejte číslo pro omezení maximální hloubky jakékoli rozhodovací strom. Zvýšení hloubku stromu může zvýšit přesnost, nese chvíli školení overfitting a vyšší.

7. **Počet náhodných rozdělí na jeden uzel**: Zadejte počet rozdělení pro použití při vytváření jednotlivých uzlů stromu. A *rozdělit* náhodně dělí prostředky, které nabízí v každé úrovni stromu (uzel).

8. **Minimální počet vzorků za uzel typu list**: Určete minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (listový) ve stromové struktuře. Zvýšením tuto hodnotu zvýšit prahovou hodnotu pro vytvoření nového pravidla.

    Výchozí hodnotu 1, ještě jeden případ může způsobit, který se má vytvořit nové pravidlo. Pokud zvýšíte hodnotu 5, museli byste trénovací data obsahují aspoň pět případy, které splňují stejných podmínek.



10. Připojte s popiskem datovou sadu a jeden z modulů školení:

    + Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](./train-model.md) modulu.

11. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete zobrazit stromu, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem na výstup [Train Model](./train-model.md) modul a vyberte **vizualizovat**.
+ Pokud chcete zobrazit pravidla pro každý uzel, klikněte na každém stromu k podrobnostem rozdělení.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 