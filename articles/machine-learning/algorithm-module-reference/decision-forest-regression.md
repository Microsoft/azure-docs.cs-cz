---
title: 'Regrese rozhodovací doménové struktury: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí modulu pro regresi rozhodovací doménové struktury ve službě Azure Machine Learning vytvořit regresní model založený na kompletu rozhodovacích stromů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b8bb3285aecb6aff399606e6263f014027a86581
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128890"
---
# <a name="decision-forest-regression-module"></a>Modul pro regresi rozhodovací doménové struktury

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit regresní model založený na kompletu rozhodovacích stromů.

Po nakonfigurování modelu je nutné proškolit model pomocí popisku DataSet a modulu [vlakového modelu](./train-model.md) .  K vytvoření předpovědi se pak dá použít trained model. 

## <a name="how-it-works"></a>Jak to funguje

Rozhodovací stromy jsou modely nevyužívající parametry, které provádějí sekvenci jednoduchých testů pro každou instanci a přecházejí do binární struktury dat stromu, dokud není dosaženo uzel na úrovni listu (rozhodnutí).

Stromy pro rozhodování mají tyto výhody:

- Jsou efektivní v výpočetních i využití paměti během školení a předpovědi.

- Můžou představovat hranice nelineárního rozhodování.

- Provádějí integrovaný výběr a klasifikaci funkcí a jsou odolné proti přítomnosti funkcí s vysokou dostupností.

Tento regresní model se skládá ze kompletu rozhodovacích stromů. U každého stromu v doménové struktuře regrese se jako předpověď vytvoří Gaussovské distribuce. Agregace se provádí přes kompletování stromů, aby bylo možné najít Gaussovské rozdělení, které je nejblíže kombinované distribuci pro všechny stromy v modelu.

Další informace o teoretickém rozhraní pro tento algoritmus a jeho implementaci najdete v tomto článku: [Doménové struktury rozhodnutí: Jednotná architektura pro klasifikaci, regresi, odhad hustoty, výukové učení a částečně pod dohledem](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak nakonfigurovat regresní model doménové struktury

1. Přidejte do experimentu modul regrese pro **rozhodovací doménovou strukturu** . Modul můžete najít v rozhraní pod **Machine Learning**, **inicializovat model**a **regrese**.

2. Otevřete vlastnosti modulu a pro **metodu převzorkování**vyberte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z **zavazadla** nebo **replikace**.

    - Zazavazadlí: Pro zaznamenání je také označováno jako *agregace Bootstrap*. Každý strom v doménové struktuře regrese má za následek Gaussovské rozdělení způsobem předpovědi. Agregaci je najít Gaussovské, jehož první dvě momenty se shodují se momentem kombinace Gaussovské, která je dána kombinací všech Gaussovskéů vrácených jednotlivými stromy.

         Další informace najdete v tématu Wikipedii pro agregaci [bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikovat**: V případě replikace je každý strom vyškolen na přesně stejných vstupních datech. Určení, které dělicí predikát se používá pro každý uzel stromu, zůstane náhodné a stromy budou odlišné.

         Další informace o procesu školení s možností **replikace** najdete v tématu [rozhodovací doménové struktury pro počítačové zpracování obrazu a analýzu lékařských imagí. Criminisi a J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    - **Jeden parametr**

      Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty. Tyto hodnoty jste pravděpodobně dozvěděli experimentováním nebo je přijali jako doprovodné materiály.



4. Pro **počet rozhodovacích stromů**určete celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se zvýší.

    > [!TIP]
    > Tato hodnota také určuje počet stromů zobrazených při vizualizaci výukového modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete hodnotu nastavit na 1. to však znamená, že bude vytvořen pouze jeden strom (strom s počáteční sadou parametrů) a žádné další iterace nebudou provedeny.

5. Pro **maximální hloubku rozhodovacích stromů**zadejte číslo, abyste omezili maximální hloubku rozhodovacího stromu. Zvýšení hloubky stromové struktury může zvýšit přesnost, a to na riziko nějakého přeložení a zvýšené doby školení.

6. Pro **počet náhodných rozdělení na uzel**zadejte počet rozdělení, který se má použít při sestavování jednotlivých uzlů stromu. *Rozdělení* znamená, že funkce v každé úrovni stromu (uzlu) jsou náhodně děleny.

7. Pro **minimální počet vzorků na uzel listu**Určete minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (list) ve stromu.

     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.


9. Připojte s popiskem datovou sadu, vyberte sloupec s jedním popiskem, který neobsahuje více než dva výsledky, a připojte se ke [výukového modelu](./train-model.md).

    - Pokud nastavíte možnost **vytvořit režim Trainer** na **jeden parametr**, provedete model pomocí modulu [výuka modelu](./train-model.md) .

   

10. Spusťte experiment.

### <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit strom, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem myši na výstup školicího modulu a vyberte **vizualizovat**.

+ Chcete-li zobrazit pravidla pro každý uzel, klikněte na jednotlivé stromové struktury a přejděte k podrobnostem o rozdělení.

+ Pokud chcete uložit snímek školicího modelu, klikněte pravým tlačítkem na výstup školicího modulu a vyberte **Uložit jako trained model**. Tato kopie modelu není aktualizována při následných spuštění experimentu. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 