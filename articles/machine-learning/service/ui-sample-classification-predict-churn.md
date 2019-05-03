---
title: 'Klasifikace: Předpověď změn, appetency a prodej nahoru '
titleSuffix: Azure Machine Learning service
description: Tento ukázkový experiment vizuální rozhraní zobrazuje binární klasifikátor předpověď změn, běžné úlohy pro řízení vztahů se zákazníky (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 1cb533348236905b7c4e9b58968041745af0e71b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028437"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Ukázka 5 – klasifikace: Předpověď změn, appetency a prodej nahoru 

Tento ukázkový experiment vizuální rozhraní zobrazuje binární klasifikátor předpověď výpovědi, appetency a nahoru – prodej, běžné úlohy pro řízení vztahů se zákazníky (CRM).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro 5 ukázkový experiment.

    ![Otevřete experiment](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Data pro tento experiment jsou z konference KDD Cup 2009. Objekt dataset obsahuje více než 50 000 řádků a sloupců 230 funkce. Úloha je k předvídání změny appetency a navýšení prodeje pro zákazníky, kteří používají tyto funkce. Najdete v článku [KDD webu](https://www.kdd.org/kdd-cup/view/kdd-cup-2009) podrobné informace o datech a úlohy.

## <a name="experiment-summary"></a>Souhrn testu

Tady je úplný experiment grafu:

![Grafem experimentu](./media/ui-sample-classification-predict-churn/experiment-graph.png)

Nejdřív jsme některé jednoduché zpracování dat.

- Nezpracovaná datová sada obsahuje velké množství chybějící hodnoty. Používáme **vyčištění chybějících dat** modulu, který chcete nahradit, chybějící hodnoty 0.

    ![Vyčistit datové sady](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funkce a odpovídající změny, appetency, a až prodej popisky jsou v různých objektech datasets. Používáme **přidat sloupce** modulu pro připojení k funkci sloupce sloupce popisek. První sloupec **Sloupec1**, je popisek sloupce. Zbývající sloupce, **Var1**, **Var2**, a tak dále sloupců funkce.
 
    ![Přidat sloupec datové sady](./media/ui-sample-classification-predict-churn/added-column1.png)

- Používáme **rozdělení dat** modulu rozdělit datovou sadu na trénování a testování sad.


    Potom jsme pomocí binární klasifikátor Boosted Decision Tree s výchozími parametry můžete vytvářet prediktivní modely. Při sestavování jednoho modelu na jeden úkol, to znamená, že jeden model/každý předpovědět prodej nahoru, appetency a změny.

## <a name="results"></a>Výsledky

Vizualizace výstupu **Evaluate Model** modulu a prohlédnout si výkon modelu v sadě testů. Pro úlohu nahoru prodej křivka roc s více TŘÍDAMI ukazuje lepší výsledky než náhodných modelu neodpovídá modelu. Oblasti pod křivkou (AUC) je 0.857. Na prahová hodnota 0,5 přesnost je 0,7, odvolání je 0.463 a skóre F1 je 0.545.

![Výsledky vyhodnocení](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Můžete přesunout **prahová hodnota** posuvník a změnit metriky pro úlohu binární klasifikace najdete v tématu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte službu k dispozici pro vizuální rozhraní ukázky:

- [Ukázka 1 - regrese: Předpovídat cenu automobilu představuje jeden](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 - regrese: Porovnání algoritmy pro předpověď cen automobilů](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpovědět úvěrové riziko](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
