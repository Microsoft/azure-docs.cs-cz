---
title: 'Klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)'
titleSuffix: Azure Machine Learning service
description: Tento článek ukazuje, jak vytvářet komplexní experimentu služby machine learning pomocí vizuální rozhraní. Se dozvíte, jak implementovat vlastní skripty Python a porovnávání několik modelů zvolit nejlepší možnost.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: efed981b500ff14a66c2355a1d14bd762000622f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606166"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)

Tento článek ukazuje, jak vytvářet komplexní experimentu služby machine learning pomocí vizuální rozhraní. Se dozvíte, jak implementovat vlastní logiku pomocí skriptů Pythonu a porovnávání několik modelů zvolit nejlepší možnost.

Tato ukázka trénovat klasifikátor předpovědět úvěrové riziko pomocí informací o aplikaci kredit jako je historie kreditu, věk a počet kreditní karty. Můžete však použít konceptů v tomto článku řešit vlastní strojového učení problémy.

Pokud právě začínáte s machine learningem, může trvat podívat [ukázka základní třídění](ui-sample-classification-predict-credit-risk-basic.md) první.

Tady je dokončené grafu pro tento experiment:

[![Graf experimentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro 4 ukázkový experiment:

    ![Otevřete experiment](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

Z úložiště UC Irvine používáme datovou sadu němčina platební karty. Tato datová sada obsahuje 1 000 vzorků s 20 funkcí a 1 popisek. Každá ukázka představuje osobu. 20 funkcí jsou zařazené do kategorií a číselné funkce. Najdete v článku [UCI webu](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) Další informace o datové sadě. Poslední sloupec je popisek, který označuje úvěrové riziko a má jenom dvě možné hodnoty: vysoké úvěrové riziko = 2 a nízké úvěrové riziko = 1.

## <a name="experiment-summary"></a>Souhrn testu

V tento experiment nám porovnat dva různé přístupy k vytváření modelů pro vyřešení tohoto problému:

- Školení s původní datové sady.
- Školení s replikované datové sady.

S oba přístupy jsme vyhodnocení modelů s použitím testovací datové sady s replikací zajistit, že výsledky jsou v souladu s náklady na funkci. Testujeme dvěma Klasifikátory pomocí obou metod: **Two-Class Support Vector Machine** a **Two-Class posíleného rozhodovacího stromu**.

Misclassifying příklad s nízkým rizikem jako vysoké náklady na je 1 a 5 je misclassifying s vysokým rizikem příkladu jako nízké náklady. Používáme **Execute Python Script** modulu pro tento chybnou nákladů.

Tady je graf testu:

[![Graf experimentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Zpracování dat

Začínáme s použitím **Editor metadat** modul a přidejte názvy sloupců, aby lépe vystihuje názvy nahraďte výchozí názvy sloupců získané z datové sady popisu na webu UCI. Nabízíme nové názvy sloupců jako hodnoty oddělené čárkami v **nový sloupec** pole název **Editor metadat**.

V dalším kroku generovat školení a testovací sady, které slouží k vývoji prediktivního modelu rizika. Jsme rozdělit na původní datovou sadu učení a testovací sady stejné velikosti pomocí **rozdělení dat** modulu. Pokud chcete vytvořit sadu stejnou velikost, nastavíme **podíl řádků v první výstupní sadě dat** možnost 0,5.

### <a name="generate-the-new-dataset"></a>Vytvořit novou datovou sadu

Protože je vysoké náklady podcenění rizika, nastavíme náklady chybnou takto:

- Pro vysoce riziková chybně klasifikované jako s nízkým rizikem případy: 5
- Pro případy s nízkým rizikem chybně klasifikované jako vysoce rizikové: 1

Tak, aby odrážela tuto funkci náklady, se vygeneruje nová datová sada. V nové datové sady každý s vysokým rizikem příklad se replikuje pětkrát, ale nemění řadu příkladů s nízkým rizikem. Data rozdělíme do datových sad trénování a testování před replikací zakázat na stejném řádku v obou sadách.

K replikaci dat s vysokou rizikovostí, máme tento kód Pythonu do **Execute Python Script** modul:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Execute Python Script** modulu replikuje učení a testovací datové sady.

### <a name="feature-engineering"></a>Návrh funkcí

**Two-Class Support Vector Machine** algoritmus vyžaduje normalizované data. Takže použijeme **normalizovat Data** modulu normalizovat všechny číselné funkce s rozsahy `tanh` transformace. A `tanh` transformace převede všechny číselné funkce na hodnoty v rozsahu 0 až 1 při zachování celkové distribuci hodnot.

**Two-Class Support Vector Machine** modul zpracuje řetězec funkcí, jejich konverze na funkce zařazené do kategorií a pak binární funkce s hodnotou 0 nebo 1. Proto nepotřebujeme normalizovat tyto funkce.

## <a name="models"></a>Modely

Protože používáme dvěma Klasifikátory **Two-Class Support Vector Machine** (SVM) a **Two-Class Boosted Decision Tree**a také použít dvě datové sady, se vygeneruje celkem čtyři modely:

- SVM trénink na původní data.
- SVM trénink na replikovaná data.
- Posílený rozhodovací strom trénink na původní data.
- Posílený rozhodovací strom natrénovaný pomocí replikovaná data.

Můžeme použít standardní experimentální pracovního postupu pro vytváření, trénování a testování modely:

1. Inicializovat learning algoritmy, pomocí **Two-Class Support Vector Machine** a **Two-Class Boosted Decision Tree**.
1. Použití **Train Model** použít algoritmus k datům a vytvoření skutečné modelu.
1. Použití **Score Model** k vytvoření skóre, které se pomocí příkladů testu.

Následující diagram ukazuje část tento experiment, ve kterém jsou použity původní a replikované školicí sady pro trénování dva různé modely SVM. **Trénování modelu** je připojené k sadě školení a **Score Model** je připojené k testovací sadě.

![Grafem experimentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

Ve fázi hodnocení experimentu jsme výpočetní přesnost každého ze čtyř modelů. Pro tento experiment používáme **Evaluate Model** k porovnání příklady, které mají stejné chybnou nákladů.

**Evaluate Model** modulu můžete vypočítat metriky výkonu pro až dva modely skóre. Takže použijeme jednu instanci **Evaluate Model** vyhodnocení dva modely SVM a jiná instance **Evaluate Model** vyhodnotit dva modely Boosted Decision Tree.

Všimněte si, že replikované test datová sada použije jako vstup pro **Score Model**. Jinými slovy skóre konečné přesnost zahrnují cenu pro získání nesprávné popisky.

## <a name="combine-multiple-results"></a>Kombinovat více výsledků

**Evaluate Model** modul vytvoří tabulku s jeden řádek, který obsahuje různé metriky. Chcete-li vytvořit jednu sadu přesnost výsledků, nejprve používáme **přidat řádky** sloučit výsledky do jedné tabulky. Použijeme následující skript jazyka Python v **Execute Python Script** modul a přidejte název modelu a školení přístup pro každý řádek v tabulce výsledků:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Výsledky

Chcete-li zobrazit výsledky testu, kliknete pravým tlačítkem na vizualizace výstupem posledního **výběr sloupců v datové sadě** modulu.

![Vizualizace výstupu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

První sloupec uvádí strojového učení algoritmus používaný ke generování modelu.
Druhý sloupec označuje typ trénovací sady.
Třetí sloupec obsahuje hodnotu citlivé přesnost.

Tyto výsledky, uvidíte, že se model, který byl vytvořen pomocí poskytuje největší přesností **Two-Class Support Vector Machine** a trénované na replikované trénovací datové sady.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte službu k dispozici pro vizuální rozhraní ukázky:

- [Ukázka 1 - regrese: Předpovídat cenu automobilu představuje jeden](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 - regrese: Porovnání algoritmy pro předpověď cen automobilů](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpovědět úvěrové riziko](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 5 – klasifikace: Předpověď výpovědi](ui-sample-classification-predict-churn.md)
- [Ukázka 6 – klasifikace: Předpověď zpoždění letu](ui-sample-classification-predict-flight-delay.md)