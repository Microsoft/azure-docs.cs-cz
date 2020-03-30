---
title: 'Rozhodnutí Forest Regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu regrese doménové struktury rozhodnutí v Azure Machine Learning vytvořit regresní model založený na souboru stromů rozhodnutí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456535"
---
# <a name="decision-forest-regression-module"></a>Rozhodovací modul regrese lesa

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření regresního modelu založeného na souboru rozhodovacích stromů.

Po konfiguraci modelu je nutné trénovat model pomocí popiskované datové sady a modulu [Model vlaku.](./train-model.md) Trénovaný model pak lze použít k předpovědi. 

## <a name="how-it-works"></a>Jak to funguje

Rozhodovací stromy jsou neparametrické modely, které provádějí posloupnost jednoduchých testů pro každou instanci a procházejí binární stromovou datovou strukturou, dokud není dosaženo uzlu listu (rozhodnutí).

Rozhodovací stromy mají tyto výhody:

- Jsou efektivní v výpočtu a využití paměti během školení a předpověď.

- Mohou představovat nelineární hranice rozhodnutí.

- Provádějí integrovaný výběr a klasifikaci funkcí a jsou odolné v přítomnosti hlučných prvků.

Tento regresní model se skládá ze souboru rozhodovacích stromů. Každý strom v regresní rozhodnutí doménové struktury výstupy Gaussova rozdělení jako předpověď. Agregace se provádí přes soubor stromů najít Gaussova distribuce nejblíže k kombinované rozdělení pro všechny stromy v modelu.

Další informace o teoretickém rámci tohoto algoritmu a jeho implementaci naleznete v tomto článku: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Jak nakonfigurovat rozhodovací model regrese doménové struktury

1. Přidejte modul **regrese doménové struktury rozhodnutí** do kanálu. Modul najdete v návrháři v části **Strojové učení**, **Inicializovat model**a **Regrese**.

2. Otevřete vlastnosti modulu a pro **metodu převzorkování**zvolte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z **pytlování** nebo **replikovat**.

    - **Pytlování**: Pytlování se také nazývá *bootstrap agregace*. Každý strom v regresní rozhodnutí doménové struktury výstupy Gaussova rozdělení jako předpověď. Agregace je najít Gausssana, jehož první dva okamžiky odpovídají momentům směsi Gaussových rozdělení daných kombinací všech distribucí vrácených jednotlivými stromy.

         Další informace naleznete v položce Wikipedie pro [agregaci Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikovat**: Při replikaci je každý strom trénován na přesně stejná vstupní data. Určení, který rozdělený predikát se používá pro každý uzel stromu zůstává náhodný a stromy budou rozmanité.

         Další informace o procesu školení pomocí možnosti **Replikovat** naleznete v [tématu Rozhodnutí v doménových strukturách pro počítačové vidění a analýzu lékařských obrázků. Criminisi a J. Shottonové. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**

    - **Jeden parametr**

      Pokud víte, jak chcete nakonfigurovat model, můžete zadat určitou sadu hodnot jako argumenty. Tyto hodnoty jste se možná naučili experimentem nebo jste je obdrželi jako vodítko.

    - **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky. 



4. V **části Počet stromů rozhodnutí**uveďte celkový počet rozhodovacích stromů, které mají být v souboru vytvořit. Vytvořením více rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale doba tréninku se zvýší.

    > [!TIP]
    > Tato hodnota také určuje počet stromů zobrazených při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete nastavit hodnotu na 1; to však znamená, že bude vytvořen pouze jeden strom (strom s počáteční sadou parametrů) a nebudou provedeny žádné další iterace.

5. Do **pole Maximální hloubka stromů rozhodnutí**zadejte číslo, které omezí maximální hloubku libovolného rozhodovacího stromu. Zvýšení hloubky stromu může zvýšit přesnost, s rizikem nadměrného vybavení a prodloužení doby tréninku.

6. Do **pole Počet náhodných rozdělení na uzel**zadejte počet rozdělení, které se mají použít při vytváření jednotlivých uzlů stromu. *Rozdělení* znamená, že prvky v každé úrovni stromu (uzlu) jsou náhodně rozděleny.

7. Pro **minimální počet vzorků na uzel listu**, uveďte minimální počet případů, které jsou nutné k vytvoření libovolného koncového uzlu (listu) ve stromu.

     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například s výchozí hodnotou 1 může i jeden případ způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, data školení by musel obsahovat alespoň pět případů, které splňují stejné podmínky.


9. Připojte označenou datovou sadu, vyberte sloupec s jedním popiskem, který neobsahuje více než dva výsledky, a připojte se k [modelu vlaku](./train-model.md).

    - Pokud nastavíte **možnost Vytvořit režim treniace** na **jeden parametr**, trénujte model pomocí modulu [Model vlaku.](./train-model.md)

   

10. Odešlete potrubí.

### <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li uložit snímek trénovaného modelu, vyberte trénovací modul a přepněte na kartu **Výstupy** v pravém panelu. Klikněte na ikonu **Registrovat model**.  Uložený model najdete jako modul ve stromu modulu. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 