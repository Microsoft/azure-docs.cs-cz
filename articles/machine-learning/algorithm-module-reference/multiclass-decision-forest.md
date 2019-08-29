---
title: 'Doménová struktura s více třídami: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul více tříd pro rozhodovací doménovou strukturu ve službě Azure Machine Learning k vytvoření modelu Machine Learning na základě algoritmu *doménové struktury rozhodnutí* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b56f08dfd1a14ffedffb612bb8974086ee08ede7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128620"
---
# <a name="multiclass-decision-forest-module"></a>Modul více tříd pro rozhodovací doménovou strukturu

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model strojového učení na základě algoritmu *rozhodovací doménové struktury* . Rozhodovací doménová struktura je model kompletování, který rychle sestaví sérii rozhodovacích stromů při učení od tagovaných dat.

## <a name="more-about-decision-forests"></a>Další informace o rozhodovacích doménových strukturách

Algoritmus doménové struktury pro rozhodování je metoda učení kompletu pro klasifikaci. Algoritmus funguje tak, že sestaví více rozhodovacích stromů a pak bude *hlasovat* na nejoblíbenější výstupní třídě. Hlasovací je forma agregace, ve které každý strom v doménové struktuře rozhodnutí klasifikace výstupuje nenormalizovaný frekvenční histogram popisků. Agregační proces sečte Tyto histogramy a normalizuje výsledek pro získání "pravděpodobnosti" pro každý popisek. V konečném rozhodnutí kompletu mají stromy, které mají důvěru vysoké předpovědi větší váhu.

Obecné rozhodovací stromy jsou modely bez ukazatelů, což znamená, že podporují data s proměnlivými distribucí. V každém stromu je spuštěná sekvence jednoduchých testů pro každou třídu, která zvyšuje úrovně stromové struktury až do dosažení koncového uzlu (rozhodnutí).

Stromy rozhodování mají mnoho výhod:

+ Můžou představovat hranice nelineárního rozhodování.
+ Jsou efektivní při výpočtech a využití paměti během školení a předpovědi.
+ Provádějí integrovaný výběr a klasifikaci funkcí.
+ Jsou odolné proti přítomnosti funkcí s vysokou dostupností.

Klasifikátor rozhodovací doménové struktury v Azure Machine Learning se skládá ze kompletu rozhodovacích stromů. Obecně platí, že modely kompletování poskytují lepší pokrytí a přesnost než jednotlivé rozhodovací stromy. Další informace najdete v tématu [rozhodovací stromy](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak konfigurovat doménovou strukturu s více třídami



1. Přidejte modul více **tříd pro rozhodovací doménovou strukturu** do experimentu v rozhraní. Tento modul najdete v části **Machine Learning**, **Inicializace modelu**a **klasifikace**.

2. Dvojitým kliknutím na modul otevřete podokno **vlastnosti** .

3. V případě **metody převzorkování**vyberte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z zavazadla nebo replikace.

    + Zazavazadlí: Pro zaznamenání je také označováno jako *agregace Bootstrap*. V této metodě se každý strom vypěstuje na novém vzorku, který vytvořil náhodným vzorkováním původní datové sady s náhradou, dokud nebudete mít datovou velikost původní. Výstupy modelů jsou kombinovány hlasováním, což je forma agregace. Další informace najdete v tématu Wikipedii pro agregaci Bootstrap.

    + **Replikovat**: V případě replikace je každý strom vyškolen na přesně stejných vstupních datech. Určení, které dělicí predikát se používá pro každý uzel stromu, zůstane náhodné a vytvoří různé stromy.

   

4. Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    + **Jeden parametr**: Tuto možnost vyberte, pokud víte, jak chcete model konfigurovat, a jako argumenty zadejte sadu hodnot.


5. **Počet rozhodovacích stromů**: Zadejte maximální počet rozhodovacích stromů, které lze v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se může zvýšit.

    Tato hodnota také určuje počet stromů zobrazených ve výsledcích při vizualizaci vyučeného modelu. Chcete-li zobrazit nebo vytisknout jeden strom, můžete nastavit hodnotu na 1. to však znamená, že lze vytvořit pouze jeden strom (strom s počáteční sadou parametrů) a nejsou provedeny žádné další iterace.

6. **Maximální hloubka rozhodovacích stromů**: Zadejte číslo pro omezení maximální hloubky jakéhokoli rozhodovacího stromu. Zvýšení hloubky stromové struktury může zvýšit přesnost, a to na riziko nějakého přeložení a zvýšené doby školení.

7. **Počet náhodných rozdělení na uzel**: Zadejte počet rozdělení, které se mají použít při vytváření jednotlivých uzlů stromu. *Rozdělení* znamená, že funkce v každé úrovni stromu (uzlu) jsou náhodně děleny.

8. **Minimální počet vzorků na uzel typu list**: Určuje minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (list) ve stromu. Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel.

    Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.



10. Připojte s popiskem datovou sadu a jeden z školicích modulů:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](./train-model.md) .

11. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit strom, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem na výstup modulu [vlakového modelu](./train-model.md) a vyberte **vizualizovat**.
+ Chcete-li zobrazit pravidla pro každý uzel, klikněte na jednotlivé stromové struktury a přejděte k podrobnostem o rozdělení.


## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 