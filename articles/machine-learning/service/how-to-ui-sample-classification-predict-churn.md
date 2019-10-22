---
title: 'Příklad vizuálního rozhraní #5: klasifikace pro předpověď změn + appetency + up-proprodej'
titleSuffix: Azure Machine Learning
description: Tento ukázkový kanál vizuálního rozhraní znázorňuje četnost změn v binárním třídění, což je běžný úkol pro řízení vztahů se zákazníky (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693560"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Ukázka 5 – klasifikace: předpověď změn, appetencyí a prodejů 

Naučte se vytvářet komplexní kanály strojového učení, aniž byste museli psát jediný řádek kódu pomocí vizuálního rozhraní.

Tento kanál vlaky doplní tři, rozpracované Klasifikátory pro **rozhodovací stromovou strukturu** pro předpověď běžných úloh pro systémy řízení vztahů se zákazníky (CRM): změny, appetency a prodej. Hodnoty dat a popisky jsou rozdělené do několika zdrojů dat a zakódované tak, aby anonymizovat informace o zákaznících, ale přesto můžeme použít vizuální rozhraní ke kombinování datových sad a výukou modelu pomocí zakrytých hodnot.

Vzhledem k tomu, že se snažíte odpovědět na otázku, kterou jste nahlásili? To se označuje jako problém klasifikace, ale můžete použít stejnou logiku jako v této ukázce, abyste mohli řešit jakýkoli typ problému strojového učení, ať už jde o regresi, klasifikaci, clusteringu a tak dále.

Zde je dokončený graf pro tento kanál:

![Graf kanálu](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro kanál Sample 5.

    ![Otevření kanálu](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Data pro tento kanál vychází z konference KDDu 2009. Má 50 000 řádků a 230 sloupců funkcí. Úkolem je předpovědět změny, appetency a prodej pro zákazníky, kteří tyto funkce používají. Další informace o datech a úkolech najdete na [webu konference KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Souhrn kanálu

Tento ukázkový kanál pro vizuální rozhraní ukazuje, jak se v rámci služby řízení vztahů se zákazníky (CRM) vytváří binární předpověď klasifikátoru pro změny, appetency a prodej.

Nejprve některé jednoduché zpracování dat.

- Nezpracovaná datová sada má mnoho chybějících hodnot. Pomocí modulu **Vyčištění chybějících dat** nahraďte chybějící hodnoty hodnotou 0.

    ![Vyčistit datovou sadu](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funkce a odpovídající změny, appetency a příprodejní štítky jsou v různých datových sadách. Pomocí modulu **Přidat sloupce** přidejte sloupce popisků ke sloupcům funkce. První sloupec, **Sloupec1**, je sloupec popisku. Zbývající sloupce, **Var1**, **Var2**a tak dále, jsou sloupce funkce.

    ![Přidat datovou sadu sloupce](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Pomocí modulu **rozdělit data** rozdělte datovou sadu do výukových a testovacích sad.

- Pak použijte binární klasifikátor zesíleného rozhodovacího stromu s výchozími parametry pro sestavení předpovědí modelů. Sestavte jeden model na každý úkol, tj. jeden model pro předpověď prodeje, appetency a změn.

## <a name="results"></a>Výsledky

Vizualizujte výstup modulu **vyhodnocení modelu** , abyste viděli výkon modelu v sadě testů. V případě úkolu, který je příprodejně k prodeji, křivka ROC ukazuje, že model je lepší než náhodný model. Oblast pod křivkou (AUC) je 0,857. U prahové hodnoty 0,5 je přesnost 0,7, odvolání je 0,463 a skóre F1 je 0,545.

![Vyhodnotit výsledky](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Můžete přesunout posuvník **prahové hodnoty** a zobrazit změny metrik pro úlohu binární klasifikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: předpověď úvěrového rizika](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-ui-sample-classification-predict-flight-delay.md)
- [Ukázka 7 – klasifikace textu: recenze knih](how-to-ui-sample-text-classification.md)
