---
title: 'Příklad návrháře #3: klasifikace pro předpověď úvěrového rizika'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet třídění strojového učení bez psaní jediného řádku kódu pomocí návrháře (Preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 2709c2e0da154a8530175144070ad560c8921b85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515535"
---
# <a name="sample-3---classification-with-feature-selection-income-prediction"></a>Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Naučte se vytvářet třídění strojového učení bez psaní jediného řádku kódu pomocí návrháře (Preview). Tato ukázka navýšení **rozhodovacího stromu se dvěma třídami** umožňuje odhadnout příjem v rámci sčítání v dospělém (> = 50 tis nebo < = 50 tis).

Vzhledem k tomu, že otázka odpovídá "který z nějakého"? označuje se jako problém klasifikace. Stejný základní postup se ale dá použít k tomu, abyste mohli řešit jakýkoli typ problému strojového učení bez ohledu na to, jestli jde o regresi, klasifikaci, clusteringu a tak dále.

Toto je konečný Graf kanálu pro tuto ukázku:

![Graf kanálu](media/how-to-ui-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Kliknutím na ukázku 3 ji otevřete.



## <a name="data"></a>Data

Datová sada obsahuje 14 funkcí a jeden sloupec popisku. Existuje několik typů funkcí, včetně číselných a kategorií. Následující diagram znázorňuje výňatek z datové sady: ![data](media/how-to-ui-sample-classification-predict-income/data.png)



## <a name="pipeline-summary"></a>Souhrn kanálu

Pomocí těchto kroků vytvořte kanál:

1. Přetáhněte na plátno kanálu modul binární datové sady pro příjem dospělého.
1. Přidejte modul **rozdělení dat** pro vytvoření školicích a testovacích sad. Nastavte zlomek řádků v první výstupní sadě dat na 0,7. Toto nastavení určuje, že 70% dat bude výstupem na levý port modulu a zbytek na správném portu. Pro účely testování používáme levou datovou sadu pro školení a tu hned.
1. Přidejte modul **výběru funkce založený na filtrech** a vyberte 5 funkcí podle PearsonCorreclation. 
1. Přidejte modul **zesíleného rozhodovacího stromu se dvěma třídami** k inicializaci klasifikátoru rozřízeného rozhodovacího stromu.
1. Přidejte modul **vlakového modelu** . Připojte třídění od předchozího kroku k levému vstupnímu portu **modelu vlaků**. Připojte filtrovanou datovou sadu z modulu výběru funkce založeného na filtru jako školicí datovou sadu.  **Model vlaků** bude zaškolit třídění.
1. Přidejte transformaci Select Columns a použijte transformační modul pro použití stejné transformace (výběr založený na výběru funkcí) pro testovací datovou sadu.
![použít-Transform](media/how-to-ui-sample-classification-predict-income/transformation.png)
1. Přidejte modul **bodového modelu** a připojte k němu modul **vlak model** . Pak přidejte testovací sadu (výstup použít modul transformace, který pro sadu testů používá výběr funkcí), do **modelu skóre**. **Model skóre** provede předpovědi. Můžete vybrat svůj výstupní port pro zobrazení předpovědi a pozitivních pravděpodobností třídy.


    Tento kanál má dva moduly skóre, ale ten na pravé straně má vyřazený sloupec Label, než provede předpověď. To je připraveno k nasazení koncového bodu v reálném čase, protože vstup webové služby očekává jenom nepopisované funkce. 

1. Přidejte modul **vyhodnocení modelu** a připojte datovou sadu ke svému levému vstupnímu portu. Pokud chcete zobrazit výsledky vyhodnocení, vyberte výstupní port modulu **vyhodnocení modelu** a vyberte **vizualizovat**.

## <a name="results"></a>Výsledky

![Vyhodnotit výsledky](media/how-to-ui-sample-classification-predict-income/evaluate-result.png)

Ve výsledcích vyhodnocení vidíte, že se jedná o křivky, jako je například ROC, přesnost odvolání a nejasná metrika. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-designer-sample-classification-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)
