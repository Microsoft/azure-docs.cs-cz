---
title: 'Klasifikace: Předvídání úvěrového rizika'
titleSuffix: Azure Machine Learning service
description: Tento ukázkový experiment vizuální rozhraní ukazuje, jak provádět binární klasifikace předpovědět úvěrové riziko podle informací uvedených v úvěr.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 3d4ec3c71aaed6bddb012fb17ee5bb96da00cd76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028527"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Ukázka 3 – klasifikace: Předvídání úvěrového rizika

Tento ukázkový experiment vizuální rozhraní ukazuje, jak provádět binární klasifikace předpovědět úvěrové riziko podle informací uvedených v úvěr. Předvádí, jak můžete provádět základní klasifikace, včetně operací zpracování dat, datové sady rozdělit učení a testovací sady, trénování modelu, skóre testovací datové a vyhodnotit předpovědi.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro 3 ukázkový experiment:

    ![Otevřete experiment](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Související ukázkové

[Ukázka 4 – klasifikace: Predikce úvěrového rizika (náklady na velikost písmen)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) poskytuje pokročilé experiment, který byl odstraněn stejnému problému jako u tohoto experimentu. Ukazuje, jak provádět _náklady na citlivé_ klasifikaci pomocí **Execute Python Script** modulu a porovnání výkonu dva binární klasifikace algoritmy. Na něj odkazovat, pokud chcete získat další informace o tom, jak vytvářet experimenty klasifikace.

## <a name="data"></a>Data

Z úložiště UC Irvine používáme datovou sadu němčina platební karty.
Datová sada obsahuje 1 000 vzorků s 20 funkcí a 1 popisek. Každá ukázka představuje osobu. Mezi funkce patří funkce číselné a kategorií. Zobrazit [UCI webu](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) pro podle kategorií funkcí. Poslední sloupec je popisek, který označuje úvěrové riziko a má jenom dvě možné hodnoty: vysoké úvěrové riziko = 2 a nízké úvěrové riziko = 1.

## <a name="experiment-summary"></a>Souhrn testu


Budeme postupovat podle těchto kroků a vytvořte experiment:

1. Přetáhněte modul UCI údajů o kreditních kartách němčina datovou sadu na plátno experimentu.
1. Přidat **upravit Metadata** modulu, takže můžeme přidat smysluplné názvy pro každý sloupec.
1. Přidat **rozdělení dat** modul k vytváření učení a testovací sady. Nastavte podíl řádků v první výstupní sadě dat k 0,7. Toto nastavení určuje, že 70 % modulů pro data bude výstup do jej s levým portem modulu a ostatní na správný port. Používáme levé datové sady pro trénování a ten správný pro testování.
1. Přidat **Two-Class Boosted Decision Tree** modulu inicializovat klasifikátor Posílený rozhodovací strom.
1. Přidat **Train Model** modulu. Připojit třídění z předchozího kroku levý vstupní port **Train Model**. Přidat sadu školení (vlevo výstupní port modulu **rozdělení dat**) na pravý vstupní port **Train Model**. **Trénování modelu** bude trénování třídění.
1. Přidat **určení skóre modelu** modulu a připojení **trénování modelu** modulu do ní. Pak přidat sadu testů (pravý port **rozdělení dat**) k **určení skóre modelu**. **Score Model** způsobí, že jsou předpovědi. Můžete vybrat jeho výstupní port předpovědí a pravděpodobnosti pozitivní třídy.
1. Přidat **Evaluate Model** modulu a připojte se k jeho s levým vstupním portem Vyhodnocená datové sady. Pokud chcete zobrazit výsledky hodnocení, vyberte na výstupní port modulu **Evaluate Model** modul a vyberte **vizualizovat**.
    
Tady je úplný experiment grafu:

![Graf experimentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)


## <a name="results"></a>Výsledky

![Výsledky vyhodnocení](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Výsledky vyhodnocení vidíte, že AUC modelu je 0.776. Na prahová hodnota 0,5 přesnost je 0.621 odvolání je 0,456 a skóre F1 je 0.526.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte službu k dispozici pro vizuální rozhraní ukázky:

- [Ukázka 1 - regrese: Předpovídat cenu automobilu představuje jeden](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 - regrese: Porovnání algoritmy pro předpověď cen automobilů](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď výpovědi](ui-sample-classification-predict-churn.md)