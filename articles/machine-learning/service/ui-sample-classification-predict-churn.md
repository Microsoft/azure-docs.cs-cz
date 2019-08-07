---
title: Mazal Předpověď změn + appetency + up-proprodej
titleSuffix: Azure Machine Learning service
description: Tento vzorový experiment mezi vizuálními rozhraními ukazuje, jak často předpověď třídění změn, což je běžný úkol pro řízení vztahů se zákazníky (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 172089d5371d8c3e38a2a22b3285b5eb180baf00
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742284"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Ukázka 5 – klasifikace: Předpověď změn, appetencyí a prodejů 

Naučte se vytvářet složitý experiment strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní.

Tento experiment vlaků tři, **dvě třídy** rozappetencyější klasifikátory rozhodovacího stromu pro předpověď běžných úloh pro systémy řízení vztahů se zákazníky (CRM): změny, a prodej. Hodnoty dat a popisky jsou rozdělené do několika zdrojů dat a zakódované tak, aby anonymizovat informace o zákaznících, ale přesto můžeme použít vizuální rozhraní ke kombinování datových sad a výukou modelu pomocí kódovaných hodnot.

Vzhledem k tomu, že se snažíme odpovědět na otázku, kterou máte? označuje se jako problém klasifikace. Stejný postup v tomto experimentu ale můžete použít k tomu, abyste mohli řešit jakýkoli typ problému strojového učení bez ohledu na to, jestli jde o regresi, klasifikaci, clusteringu a tak dále.

Zde je dokončený graf pro tento experiment:

![Graf experimentu](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro experiment Sample 5.

    ![Otevřít experiment](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Data, která pro tento experiment použijete, jsou od konference KDD. 2009. Datová sada obsahuje 50 000 řádků a 230 sloupců funkcí. Úkolem je předpovědět změny, appetency a prodej pro zákazníky, kteří tyto funkce používají. Další informace o datech a úkolech najdete na [webu konference KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Shrnutí experimentů

Tento vzorový experiment mezi vizuálními rozhraními ukazuje, jak se v programu pro správu vztahů se zákazníky (CRM) zobrazuje binární klasifikátor pro třídění změn, appetency a prodej.

Nejdřív máme jednoduché zpracování dat.

- Nezpracovaná datová sada obsahuje spoustu chybějících hodnot. K nahrazení chybějících hodnot hodnotou 0 používáme modul **Vyčištění chybějících dat** .

    ![Vyčistit datovou sadu](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funkce a odpovídající změny, appetency a příprodejní štítky jsou v různých datových sadách. K připojení sloupců s popisky ke sloupcům funkce používáme modul **Přidat sloupce** . První sloupec, **Sloupec1**, je sloupec popisku. Zbývající sloupce, **Var1**, **Var2**a tak dále, jsou sloupce funkce.

    ![Přidat datovou sadu sloupce](./media/ui-sample-classification-predict-churn/added-column1.png)

- K rozdělení datové sady do výukových a testovacích sad používáme modul **rozdělit data** .

    Pak použijeme binární klasifikátor rozvětvení rozhodovacího stromu s výchozími parametry pro sestavení předpovědí modelů. Sestavíme jeden model na každý úkol, tj. jeden model pro předpověď prodeje, appetency a změn.

## <a name="results"></a>Výsledky

Vizualizujte výstup modulu **vyhodnocení modelu** , abyste viděli výkon modelu v sadě testů. V případě úkolu, který je příprodejně k prodeji, křivka ROC ukazuje, že model je lepší než náhodný model. Oblast pod křivkou (AUC) je 0,857. U prahové hodnoty 0,5 je přesnost 0,7, odvolání je 0,463 a skóre F1 je 0,545.

![Vyhodnotit výsledky](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Můžete přesunout posuvník **prahové hodnoty** a zobrazit změny metrik pro úlohu binární klasifikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: Předpověď ceny automobilu](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 – regrese: Porovnat algoritmy pro předpověď cen automobilu](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpověď úvěrového rizika](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 6 – klasifikace: Předpověď zpoždění letů](ui-sample-classification-predict-flight-delay.md)