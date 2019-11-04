---
title: 'Regrese rozhodovací doménové struktury: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu pro regresi rozhodovací doménové struktury v Azure Machine Learning vytvořit regresní model založený na kompletu rozhodovacích stromů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d930a6d856c6608e7792ce8ef3204b39aba0028a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497963"
---
# <a name="decision-forest-regression-module"></a>Modul pro regresi rozhodovací doménové struktury

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete vytvořit regresní model založený na kompletu rozhodovacích stromů.

Po nakonfigurování modelu je nutné proškolit model pomocí popisku DataSet a modulu [vlakového modelu](./train-model.md) . K vytvoření předpovědi se pak dá použít trained model. 

## <a name="how-it-works"></a>Jak to funguje

Rozhodovací stromy jsou modely nevyužívající parametry, které provádějí sekvenci jednoduchých testů pro každou instanci a přecházejí do binární struktury dat stromu, dokud není dosaženo uzel na úrovni listu (rozhodnutí).

Stromy pro rozhodování mají tyto výhody:

- Jsou efektivní v výpočetních i využití paměti během školení a předpovědi.

- Můžou představovat hranice nelineárního rozhodování.

- Provádějí integrovaný výběr a klasifikaci funkcí a jsou odolné proti přítomnosti funkcí s vysokou dostupností.

Tento regresní model se skládá ze kompletu rozhodovacích stromů. U každého stromu v doménové struktuře regrese se jako předpověď vytvoří Gaussovské distribuce. Agregace se provádí přes kompletování stromů, aby bylo možné najít Gaussovské rozdělení, které je nejblíže kombinované distribuci pro všechny stromy v modelu.

Další informace o teoretickém rozhraní tohoto algoritmu a jeho implementaci najdete v tomto článku: [rozhodovací doménové struktury: jednotná architektura pro klasifikaci, regresi, odhad hustoty, učení kurzů a vzdělávání s částečně kontrolovaným učením. ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak nakonfigurovat regresní model doménové struktury

1. Přidejte do kanálu modul **regrese rozhodovací doménové struktury** . Modul můžete najít v návrháři v části **Machine Learning**, **inicializovat model**a **regresi**.

2. Otevřete vlastnosti modulu a pro **metodu převzorkování**vyberte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z **zavazadla** nebo **replikace**.

    - Zaznamenání **do zavazadla**se také označuje jako *agregace Bootstrap*. Každý strom v doménové struktuře regrese má za následek Gaussovské rozdělení způsobem předpovědi. Agregace je najít Gaussovské, jehož první dvě momenty odpovídá momentu kombinace Gaussovské distribuce, která je dána kombinací všech distribucí vrácených jednotlivými stromy.

         Další informace najdete v tématu Wikipedii pro [agregaci Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikace: v**rámci replikace je každý strom vyškolen na přesně stejných vstupních datech. Určení, které dělicí predikát se používá pro každý uzel stromu, zůstane náhodné a stromy budou odlišné.

         Další informace o procesu školení s možností **replikace** najdete v tématu [rozhodovací doménové struktury pro počítačové zpracování obrazu a analýzu lékařských imagí. Criminisi a J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/)..

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

   

10. Spuštění kanálu

### <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit strom, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem myši na výstup školicího modulu a vyberte **vizualizovat**.

+ Chcete-li zobrazit pravidla pro každý uzel, klikněte na jednotlivé stromové struktury a přejděte k podrobnostem o rozdělení.

+ Pokud chcete uložit snímek školicího modelu, klikněte pravým tlačítkem na výstup školicího modulu a vyberte **Uložit jako trained model**. Tato kopie modelu není aktualizována při následných spuštěních kanálu. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 