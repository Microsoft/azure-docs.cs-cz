---
title: 'Ukázka vizuálního rozhraní #4: Klasifikace pro předpověď úvěrového rizika (citlivé na náklady)'
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte, jak vytvořit složitý experiment strojového učení pomocí vizuálního rozhraní. Naučíte se, jak implementovat vlastní skripty v Pythonu a porovnat více modelů a vybrat nejlepší možnost.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 977ff101b0f697a48b3e5595834c98fef0f1119a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997030"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)

V tomto článku se dozvíte, jak vytvořit složitý experiment strojového učení pomocí vizuálního rozhraní. Naučíte se, jak implementovat vlastní logiku pomocí skriptů Pythonu a porovnat více modelů a vybrat nejlepší možnost.

Tato ukázka navlakuje klasifikátor k předpovídání úvěrového rizika pomocí informací o kreditních aplikacích, jako je například historie kreditů, stáří a počet platebních karet. Koncepty v tomto článku ale můžete použít k tomu, abyste se mohli vypořádat s vlastními problémy machine learningu.

Pokud se strojové učení teprve začíná, můžete si nejdřív prohlédnout [základní vzorek třídění](ui-sample-classification-predict-credit-risk-basic.md) .

Zde je dokončený graf pro tento experiment:

[![Graf experimentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro experiment Sample 4:

    ![Otevřít experiment](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

V úložišti UC Irvine používáme datovou sadu německé kreditní karty. Tato datová sada obsahuje 1 000 vzorků s 20 funkcemi a 1 popiskem. Každá ukázka představuje osobu. 20 funkcí zahrnuje číselné a kategorií funkce. Další informace o datové sadě najdete na [webu UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . Poslední sloupec je popisek, který označuje úvěrové riziko a má pouze dvě možné hodnoty: vysoké úvěrové riziko = 2 a nízké úvěrové riziko = 1.

## <a name="experiment-summary"></a>Shrnutí experimentů

V tomto experimentu porovnáme dva různé přístupy k vygenerování modelů pro vyřešení tohoto problému:

- Školení s původní datovou sadou.
- Školení s replikovanou datovou sadou.

Oba přístupy vyhodnotí modely pomocí testovací datové sady s replikací, aby bylo zajištěno, že výsledky budou zarovnány s funkcí cost. Testujeme dva třídění podle obou přístupů: **Podpora dvou tříd – vektorový počítač** a se **dvěma třídami se zvyšuje rozhodovací strom**.

Náklady na chybnou klasifikaci příkladu s nízkým rizikem jako vysoké jsou 1 a náklady na neklasifikaci vysoce rizikového příkladu, který je nízký, je 5. Pro tyto náklady na nesprávnou klasifikaci používáme modul **vykonávání skriptu Pythonu** .

Tady je graf experimentu:

[![Graf experimentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Zpracování dat

Začneme pomocí modulu **Editor metadat** k přidání názvů sloupců, které nahradí výchozí názvy sloupců s více smysluplnými názvy získanými z popisu datové sady na webu UCI. Nové názvy sloupců poskytujeme jako hodnoty oddělené čárkami v poli **nový název sloupce** **editoru metadat**.

Dále vygenerujeme školicí a testovací sady, které se používají pro vývoj modelu předpovědi rizik. Původní datovou sadu rozdělíme na školicí a testovací sady se stejnou velikostí pomocí modulu **rozdělit data** . Chcete-li vytvořit sady se stejnou velikostí, nastavíme **zlomky řádků v první výstupní sadě dat** na 0,5.

### <a name="generate-the-new-dataset"></a>Generovat novou datovou sadu

Vzhledem k tomu, že náklady na nebezpečí pododhadu jsou vysoké, nastavili jsme náklady na chybnou klasifikaci takto:

- U vysoce rizikových případů nesprávně klasifikovaných jako nízké riziko: 5
- Pro případy s nízkým rizikem klasifikované jako vysoké riziko: 1

Aby odrážel tuto funkci nákladů, vygenerujeme novou datovou sadu. V nové datové sadě se každý příklad s vysokým rizikem replikuje pětkrát, ale počet příkladů s nízkým rizikem se nezmění. Data rozdělíme na školení a testování datových sad před replikací, aby se zabránilo tomu, aby byl stejný řádek v obou sadách.

Chcete-li replikovat data s vysokým rizikem, vložte tento kód Pythonu do modulu **spuštění skriptu Pythonu** :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Modul **spuštění skriptu Pythonu** replikuje jak školicí, tak testovací datové sady.

### <a name="feature-engineering"></a>Návrh funkcí

Algoritmus **vektorového stroje podpory dvou tříd** vyžaduje normalizovaná data. Proto používáme modul **normalizing data** k normalizaci rozsahů všech číselných funkcí pomocí `tanh` transformace. `tanh` Transformace převede všechny číselné funkce na hodnoty v rozsahu 0 až 1 a současně zachovává celkovou distribuci hodnot.

Modul **vektorového stroje podpory dvou tříd** zpracovává řetězcové funkce, převádí je na funkce kategorií a pak na binární funkce s hodnotou 0 nebo 1. Proto není nutné tyto funkce normalizovat.

## <a name="models"></a>Modely

Vzhledem k tomu, že použijeme dva klasifikátory, **Mezitřídní vektorový počítač** (SVM) a **dvakrát rozhodovací strom se dvěma třídami**a použijeme také dvě datové sady, vygenerujeme celkem čtyři modely:

- SVM se vyškolená s původními daty.
- SVM je vyškolená pro replikovaná data.
- Posílený rozhodovací strom byl vyučen s původními daty.
- Posílený rozhodovací strom byl vyučen pomocí replikovaných dat.

Pro vytváření, analýzu a testování modelů používáme standardní experimentální pracovní postup:

1. Inicializujte algoritmy pro učení pomocí **dvou tříd – vektorový počítač podpory** a se **dvěma třídami se zvyšuje rozhodovací strom**.
1. Použijte **model výuky** pro použití algoritmu pro data a vytvořte skutečný model.
1. Použijte **model skóre** k vytvoření skóre pomocí příkladů testu.

Následující diagram znázorňuje část tohoto experimentu, ve které se původní a replikované školicí sady používají ke školení dvou různých SVM modelů. **Model výuky** je připojen ke školicí sadě a **model skóre** je připojen k sadě testů.

![Graf experimentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

Ve fázi hodnocení experimentu vypočítáme přesnost každého ze čtyř modelů. Pro účely tohoto experimentu používáme **model Evaluate** k porovnání příkladů, které mají stejné náklady na klasifikaci.

Modul **vyhodnocení modelu** může vypočítat metriky výkonu, a to až pro dva modely skóre. K vyhodnocení dvou modelů SVM a jiné instance **modelu vyhodnocení** proto používáme jednu instanci **hodnocení** , aby bylo možné vyhodnotit dva modely zesíleného rozhodovacího stromu.

Všimněte si, že se replikovaná testovací datová sada používá jako vstup pro **model skóre**. Jinými slovy, konečné skóre přesnosti zahrnuje náklady na získání špatných popisků.

## <a name="combine-multiple-results"></a>Kombinovat více výsledků

Modul **vyhodnocení modelu** vytvoří tabulku s jedním řádkem, který obsahuje různé metriky. Chcete-li vytvořit jednu sadu výsledků přesnosti, nejprve použijte příkaz **Přidat řádky** pro zkombinování výsledků do jedné tabulky. Potom pomocí následujícího skriptu Pythonu v modulu **spouštění skriptu Pythonu** přidáte název modelu a postup pro školení pro každý řádek v tabulce výsledků:

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

Chcete-li zobrazit výsledky experimentu, můžete kliknout pravým tlačítkem myši na výstup vizualizace v modulu poslední **Výběr sloupců v datové sadě** .

![Vizualizovat výstup](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

První sloupec uvádí algoritmus strojového učení, který se používá k vygenerování modelu.
Druhý sloupec indikuje typ sady školení.
Třetí sloupec obsahuje hodnotu přesnosti citlivou na náklady.

Z těchto výsledků vidíte, že je nejlepší přesnost zajištěna modelem vytvořeným se **dvěma třídami pro vektorový stroj podpory** a vyškolenou na replikovanou datovou sadu školení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: Předpověď ceny automobilu](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 – regrese: Porovnat algoritmy pro předpověď cen automobilu](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpověď úvěrového rizika](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 5 – klasifikace: Předpověď změn](ui-sample-classification-predict-churn.md)
- [Ukázka 6 – klasifikace: Předpověď zpoždění letů](ui-sample-classification-predict-flight-delay.md)
