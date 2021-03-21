---
title: 'Více tříd – rozhodovací doménová struktura: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul více tříd pro rozhodovací doménovou strukturu v Azure Machine Learning k vytvoření modelu machine learningu na základě algoritmu *doménové struktury rozhodnutí* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 1be66bdd8a1cf25a32ad3102d770078c904c4b6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94376821"
---
# <a name="multiclass-decision-forest-module"></a>Modul více tříd pro rozhodovací doménovou strukturu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

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

1. Přidejte modul **doménové struktury s více třídami** do kanálu v návrháři. Tento modul najdete v části **Machine Learning**, **Inicializace modelu** a **klasifikace**.

2. Dvojitým kliknutím na modul otevřete podokno **vlastnosti** .

3. V případě **metody převzorkování** vyberte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z zavazadla nebo replikace.

    + Zaznamenání **do zavazadla** se také označuje jako *agregace Bootstrap*. V této metodě se každý strom vypěstuje na novém vzorku, který vytvořil náhodným vzorkováním původní datové sady s náhradou, dokud nebudete mít datovou velikost původní. Výstupy modelů jsou kombinovány *hlasováním*, což je forma agregace. Další informace najdete v tématu Wikipedii pro agregaci Bootstrap.

    + **Replikace: v** rámci replikace je každý strom vyškolen na přesně stejných vstupních datech. Určení, které dělicí predikát se používá pro každý uzel stromu, zůstane náhodné a vytvoří různé stromy.

   

4. Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    + **Jeden parametr**: tuto možnost vyberte, pokud víte, jak chcete model konfigurovat, a jako argumenty zadejte sadu hodnot.

    + **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.   

5. **Počet rozhodovacích stromů**: zadejte maximální počet rozhodovacích stromů, které lze v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se může zvýšit.

    Pokud nastavíte hodnotu 1; to však znamená, že lze vytvořit pouze jeden strom (strom s počáteční sadou parametrů) a nejsou provedeny žádné další iterace.

6. **Maximální hloubka rozhodovacích stromů**: zadejte číslo pro omezení maximální hloubky jakéhokoli rozhodovacího stromu. Zvýšení hloubky stromové struktury může zvýšit přesnost, a to na riziko nějakého přeložení a zvýšené doby školení.

7. **Počet náhodných rozdělení na uzel**: zadejte počet rozdělení, který se použije při sestavování jednotlivých uzlů stromu. *Rozdělení* znamená, že funkce v každé úrovni stromu (uzlu) jsou náhodně děleny.

8. **Minimální počet vzorků na uzel na list**: Určuje minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (list) ve stromu. Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel.

    Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.



10. Propojit s popiskem datovou sadu a vyškolit model:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.

11. Odešlete kanál.



## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 