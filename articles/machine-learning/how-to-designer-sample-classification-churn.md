---
title: 'Návrhář: příklad prediktivních změn'
titleSuffix: Azure Machine Learning
description: Pomocí tohoto příkladu klasifikace můžete předpovědět změny v Návrháři Azure Machine Learning & zvýšili rozhodovací stromy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 88f688608a0ae3d435699362f9326c7c02d494a4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311108"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Použití zesíleného rozhodovacího stromu k předpovědi změn pomocí návrháře Azure Machine Learning

**Ukázka návrháře 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Naučte se vytvářet komplexní kanály strojového učení bez nutnosti psát jediný řádek kódu pomocí návrháře.

Tento kanál vlaků 2 se **dvěma třídami zvyšuje klasifikátory rozhodovacího stromu** , aby bylo možné předpovědět běžné úkoly pro systémy řízení vztahů se zákazníky (CRM) – změny zákazníků. Hodnoty dat a popisky jsou rozdělené do několika zdrojů dat a zakódované tak, aby se anonymizovat informace o zákaznících, ale i přesto můžete Návrhář použít ke kombinování datových sad a výukou modelu pomocí zakrytých hodnot.

Vzhledem k tomu, že se snažíte odpovědět na otázku, kterou jste nahlásili? To se označuje jako problém klasifikace, ale můžete použít stejnou logiku jako v této ukázce, abyste mohli řešit jakýkoli typ problému strojového učení, ať už jde o regresi, klasifikaci, clusteringu a tak dále.

Zde je dokončený graf pro tento kanál:

![Graf kanálu](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknutím na tlačítko Ukázka 5 otevřete. 

## <a name="data"></a>Data

Data pro tento kanál vychází z konference KDDu 2009. Má 50 000 řádků a 230 sloupců funkcí. Úkolem je předpovědět změny, appetency a prodej pro zákazníky, kteří tyto funkce používají. Další informace o datech a úkolech najdete na [webu konference KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Souhrn kanálu

Tento vzorový kanál v Návrháři zobrazuje ukázku klasifikátoru pro změny, appetency a prodej, což je společný úkol pro řízení vztahů se zákazníky (CRM).

Nejprve některé jednoduché zpracování dat.

- Nezpracovaná datová sada má mnoho chybějících hodnot. Pomocí modulu **Vyčištění chybějících dat** nahraďte chybějící hodnoty hodnotou 0.

    ![Vyčistit datovou sadu](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- Funkce a odpovídající změny jsou v různých datových sadách. Pomocí modulu **Přidat sloupce** přidejte sloupce popisků ke sloupcům funkce. První sloupec, **Sloupec1**, je sloupec popisku. Z výsledku vizualizace můžeme vidět, že datová sada je nevyvážená. Způsob více negativních (-1) příkladů než pozitivních příkladů (+ 1). Použijeme modul **SMOTE** k pozdějšímu zvýšení odstupujících případů.

    ![Přidat datovou sadu sloupce](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- Pomocí modulu **rozdělit data** rozdělte datovou sadu do výukových a testovacích sad.

- Pak použijte binární klasifikátor zesíleného rozhodovacího stromu s výchozími parametry pro sestavení předpovědí modelů. Sestavte jeden model na každý úkol, tj. jeden model pro předpověď prodeje, appetency a změn.

- V pravé části kanálu používáme modul **SMOTE** ke zvýšení procenta pozitivních příkladů. SMOTE procento je nastavené na 100, aby bylo možné zdvojnásobit kladné příklady. Přečtěte si další informace o tom, jak modul SMOTE funguje s [modulem SMOTE reference0](algorithm-module-reference/smote.md).

## <a name="results"></a>Výsledky

Vizualizujte výstup modulu **vyhodnocení modelu** , abyste viděli výkon modelu v sadě testů. 

![Vyhodnocení výsledků](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 Můžete přesunout posuvník **prahové hodnoty** a zobrazit změny metrik pro úlohu binární klasifikace. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](how-to-designer-sample-classification-predict-income.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)
