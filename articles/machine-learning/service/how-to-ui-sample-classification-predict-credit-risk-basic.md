---
title: 'Příklad vizuálního rozhraní #3: klasifikace pro předpověď úvěrového rizika'
titleSuffix: Azure Machine Learning
description: Naučte se sestavovat třídění strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693506"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Ukázka 3 – klasifikace: předpověď úvěrového rizika

Naučte se sestavovat třídění strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní. V tomto ukázkovém kanálu se na základě informací o kreditních kartách, jako je historie kreditů, stáří a počet platebních karet, **rozzvýšil rozhodovací strom o dvou třídách** .

Vzhledem k tomu, že otázka odpovídá "který z nějakého"? označuje se jako problém klasifikace. Stejný základní postup se ale dá použít k tomu, abyste mohli řešit jakýkoli typ problému strojového učení bez ohledu na to, jestli jde o regresi, klasifikaci, clusteringu a tak dále.

Toto je konečný Graf kanálu pro tuto ukázku:

![Graf kanálu](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro kanál Sample 3:

    ![Otevření kanálu](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Související ukázka

[Ukázka 4-klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) poskytuje pokročilý kanál, který řeší stejný problém jako v této ukázce. Ukazuje, jak provést klasifikaci *citlivou na náklady* pomocí modulu **spuštění skriptu Pythonu** a porovnat výkon dvou binárních klasifikačních algoritmů. V případě, že se chcete dozvědět víc o tom, jak vytvářet klasifikační kanály, najdete je tady.


## <a name="data"></a>Data

Ukázka používá datovou sadu německé kreditní karty z úložiště UC Irvine. Obsahuje 1 000 vzorků s 20 funkcemi a 1 popiskem. Každá ukázka představuje osobu. Mezi tyto funkce patří číselné a kategorií funkce. Význam funkcí kategorií najdete na [webu UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . Poslední sloupec je popisek, který označuje úvěrové riziko a má pouze dvě možné hodnoty: vysoké úvěrové riziko = 2 a nízké úvěrové riziko = 1.

## <a name="pipeline-summary"></a>Souhrn kanálu

Pomocí těchto kroků vytvořte kanál:

1. Přetáhněte modul datové sady UCI do německé platební karty do plátna kanálu.
1. Přidejte modul **Upravit metadata** , abychom mohli přidat smysluplné názvy pro každý sloupec.
1. Přidejte modul **rozdělení dat** pro vytvoření školicích a testovacích sad. Nastavte zlomek řádků v první výstupní sadě dat na 0,7. Toto nastavení určuje, že 70% dat bude výstupem na levý port modulu a zbytek na správném portu. Pro účely testování používáme levou datovou sadu pro školení a tu hned.
1. Přidejte modul **zesíleného rozhodovacího stromu se dvěma třídami** k inicializaci klasifikátoru rozřízeného rozhodovacího stromu.
1. Přidejte modul **vlakového modelu** . Připojte třídění od předchozího kroku k levému vstupnímu portu **modelu vlaků**. Přidejte sadu školení (levý výstupní port **rozdělených dat**) ke správnému vstupnímu portu **modelu vlaku**. **Model vlaků** bude zaškolit třídění.
1. Přidejte modul určení **skóre modelu** a připojte k němu modul **vlak model** . Pak přidejte sadu testů (pravý port **rozdělených dat**) do **modelu skóre**. **Model skóre** provede předpovědi. Můžete vybrat svůj výstupní port pro zobrazení předpovědi a pozitivních pravděpodobností třídy.
1. Přidejte modul **vyhodnocení modelu** a připojte datovou sadu ke svému levému vstupnímu portu. Pokud chcete zobrazit výsledky vyhodnocení, vyberte výstupní port modulu **vyhodnocení modelu** a vyberte **vizualizovat**.

## <a name="results"></a>Výsledky

![Vyhodnotit výsledky](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Ve výsledcích hodnocení uvidíte, že AUC modelu je 0,776. U prahové hodnoty 0,5 je přesnost 0,621, odvolání je 0,456 a skóre F1 je 0,526.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-ui-sample-classification-predict-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-ui-sample-classification-predict-flight-delay.md)
- [Ukázka 7 – klasifikace textu: recenze knih](how-to-ui-sample-text-classification.md)