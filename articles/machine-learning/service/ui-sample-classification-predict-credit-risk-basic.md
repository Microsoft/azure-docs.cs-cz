---
title: 'Ukázka vizuálního rozhraní #3: Klasifikace pro předpověď úvěrového rizika'
titleSuffix: Azure Machine Learning service
description: Naučte se sestavovat třídění strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7565f94910d0e926682a72af42b02059fe7295ea
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990053"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Ukázka 3 – klasifikace: Predikce úvěrového rizika

Naučte se sestavovat třídění strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní. Tato ukázka nakládá se dvěma třídami posíleného **rozhodovacího stromu** pro předpověď úvěrového rizika (vysoké nebo nízké) na základě informací o kreditních aplikacích, jako je historie kreditů, stáří a počet platebních karet.

Vzhledem k tomu, že se snažíme odpovědět na otázku, kterou máte? označuje se jako problém klasifikace. Stejný základní postup se ale dá použít k tomu, abyste mohli řešit jakýkoli typ problému strojového učení bez ohledu na to, jestli jde o regresi, klasifikaci, clusteringu a tak dále.

Zde je dokončený graf pro tento experiment:

![Graf experimentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro zkoušku Sample 3:

    ![Otevřít experiment](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Související ukázka

[Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na](ui-sample-classification-predict-credit-risk-cost-sensitive.md) náklady) poskytuje pokročilý experiment, který vyřeší stejný problém jako tento experiment. Ukazuje, jak provést klasifikaci _citlivou na náklady_ pomocí modulu **spuštění skriptu Pythonu** a porovnat výkon dvou binárních klasifikačních algoritmů. Pokud chcete získat další informace o tom, jak sestavit experimenty klasifikace, přečtěte si to.

## <a name="data"></a>Data

V úložišti UC Irvine používáme datovou sadu německé kreditní karty.
Datová sada obsahuje 1 000 vzorků s 20 funkcemi a 1 popiskem. Každá ukázka představuje osobu. Mezi tyto funkce patří číselné a kategorií funkce. Význam funkcí kategorií najdete na [webu UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . Poslední sloupec je popisek, který označuje úvěrové riziko a má pouze dvě možné hodnoty: vysoké úvěrové riziko = 2 a nízké úvěrové riziko = 1.

## <a name="experiment-summary"></a>Shrnutí experimentů

Pomocí těchto kroků vytvoříte experiment:

1. Přetáhněte na plátno experimentu modul datové sady UCI pro německé platební karty.
1. Přidejte modul **Upravit metadata** , abychom mohli přidat smysluplné názvy pro každý sloupec.
1. Přidejte modul **rozdělení dat** pro vytvoření školicích a testovacích sad. Nastavte zlomek řádků v první výstupní sadě dat na 0,7. Toto nastavení určuje, že 70% dat bude výstupem na levý port modulu a zbytek na správném portu. Pro účely testování používáme levou datovou sadu pro školení a tu hned.
1. Přidejte modul **zesíleného rozhodovacího stromu se dvěma třídami** k inicializaci klasifikátoru rozřízeného rozhodovacího stromu.
1. Přidejte modul **vlakového modelu** . Připojte třídění od předchozího kroku k levému vstupnímu portu **modelu vlaků**. Přidejte sadu školení (levý výstupní port **rozdělených dat**) ke správnému vstupnímu portu **modelu vlaku**. **Model vlaků** bude zaškolit třídění.
1. Přidejte modul určení **skóre modelu** a připojte k němu modul **vlak model** . Pak přidejte sadu testů (pravý port **rozdělených dat**) do **modelu skóre**. **Model skóre** provede předpovědi. Můžete vybrat svůj výstupní port pro zobrazení předpovědi a pozitivních pravděpodobností třídy.
1. Přidejte modul **vyhodnocení modelu** a připojte datovou sadu ke svému levému vstupnímu portu. Pokud chcete zobrazit výsledky vyhodnocení, vyberte výstupní port modulu **vyhodnocení modelu** a vyberte **vizualizovat**.

Tady je kompletní graf experimentu:

![Graf experimentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Výsledky

![Vyhodnotit výsledky](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Ve výsledcích hodnocení uvidíte, že AUC modelu je 0,776. U prahové hodnoty 0,5 je přesnost 0,621, odvolání je 0,456 a skóre F1 je 0,526.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: Předpověď ceny automobilu](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 – regrese: Porovnat algoritmy pro předpověď cen automobilu](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď změn](ui-sample-classification-predict-churn.md)
- [Ukázka 6 – klasifikace: Předpověď zpoždění letů](ui-sample-classification-predict-flight-delay.md)
