---
title: 'Návrhář: příklad prediktivního úvěrového rizika'
titleSuffix: Azure Machine Learning
description: Sestavujte klasifikátor a pomocí vlastních skriptů v Pythonu můžete předpovědět úvěrové riziko pomocí návrháře Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 54ca0df005dccceacc88044a51f31ad784b7071b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763398"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Vytvoření klasifikátoru & použití skriptů Pythonu k předpovídání úvěrového rizika pomocí návrháře Azure Machine Learning

**Návrhář (Preview) – ukázka 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit komplexní kanál strojového učení pomocí návrháře (Preview). Naučíte se, jak implementovat vlastní logiku pomocí skriptů Pythonu a porovnat více modelů a vybrat nejlepší možnost.

Tato ukázka navlakuje klasifikátor k předpovídání úvěrového rizika pomocí informací o kreditních aplikacích, jako je například historie kreditů, stáří a počet platebních karet. Koncepty v tomto článku ale můžete použít k tomu, abyste se mohli vypořádat s vlastními problémy machine learningu.

Zde je dokončený graf pro tento kanál:

[![Graf kanálu](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknutím na položku ukázka 4 otevřete.

## <a name="data"></a>Data

V této ukázce se používá datová sada pro německé kreditní karty z úložiště UC Irvine. Obsahuje 1 000 vzorků s 20 funkcemi a jedním štítkem. Každá ukázka představuje osobu. 20 funkcí zahrnuje číselné a kategorií funkce. Další informace o datové sadě najdete na [webu UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). Poslední sloupec je popisek, který označuje úvěrové riziko a má pouze dvě možné hodnoty: vysoké úvěrové riziko = 2 a nízké úvěrové riziko = 1.

## <a name="pipeline-summary"></a>Souhrn kanálu

V tomto kanálu porovnáte dva různé přístupy k vygenerování modelů pro vyřešení tohoto problému:

- Školení s původní datovou sadou.
- Školení s replikovanou datovou sadou.

Oba přístupy vyhodnotí modely pomocí testovací datové sady s replikací, aby bylo zajištěno, že výsledky budou zarovnány s funkcí cost. Test dvou klasifikátorů s oběma přístupy: **Podpora dvou tříd – vektorový počítač** a rozposílený **rozhodovací strom se dvěma třídami**.

Náklady na chybnou klasifikaci příkladu s nízkým rizikem jako vysoké jsou 1 a náklady na neklasifikaci vysoce rizikového příkladu, který je nízký, je 5. Pro tyto náklady na nesprávnou klasifikaci používáme modul **vykonávání skriptu Pythonu** .

Tady je graf kanálu:

[![Graf kanálu](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Zpracování dat

Začněte pomocí modulu **Editor metadat** a přidejte názvy sloupců, abyste nahradili výchozí názvy sloupců, které mají smysluplné názvy, a to z popisu datové sady na webu UCI. Nové názvy sloupců zadejte jako hodnoty oddělené čárkami v poli **nový název sloupce** **editoru metadat**.

Dále vygenerujte školicí a testovací sady použité pro vývoj modelu předpovědi rizik. Rozdělte původní datovou sadu do školicích a testovacích sad se stejnou velikostí pomocí modulu **rozdělit data** . Chcete-li vytvořit sady se stejnou velikostí, nastavte **zlomky řádků v první výstupní sadě dat** na 0,7.

### <a name="generate-the-new-dataset"></a>Generovat novou datovou sadu

Vzhledem k tomu, že náklady na nebezpečí pododhadu jsou vysoké, nastavte náklady na neplatnou klasifikaci takto:

- U vysoce rizikových případů nesprávně klasifikovaných jako nízké riziko: 5
- Pro případy s nízkým rizikem klasifikované jako vysoké riziko: 1

Chcete-li tuto funkci nákladů odrážet, vygenerujte novou datovou sadu. V nové datové sadě se každý příklad s vysokým rizikem replikuje pětkrát, ale počet příkladů s nízkým rizikem se nezmění. Před replikací rozdělte data na školení a testování datových sad, aby se zabránilo tomu, aby byl stejný řádek v obou sadách.

Pokud chcete replikovat data s vysokým rizikem, vložte tento kód Pythonu do modulu **spuštění skriptu Pythonu** :

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

Algoritmus **vektorového stroje podpory dvou tříd** vyžaduje normalizovaná data. Proto použijte modul **Normalize data** pro normalizaci rozsahů všech numerických funkcí pomocí transformace `tanh`. Transformace `tanh` převede všechny číselné funkce na hodnoty v rozsahu 0 až 1 a současně zachovává celkovou distribuci hodnot.

Modul **vektorového stroje podpory dvou tříd** zpracovává řetězcové funkce, převádí je na funkce kategorií a pak na binární funkce s hodnotou nula nebo One. Takže tyto funkce nemusíte normalizovat.

## <a name="models"></a>Modely

Vzhledem k tomu, že jste použili dva klasifikátory, **Mezitřídní vektorový stroj** (SVM) a **dvakrát rozhodovací strom se dvěma třídami**a dvě datové sady, vygenerujete celkem čtyři modely:

- SVM se vyškolená s původními daty.
- SVM je vyškolená pro replikovaná data.
- Posílený rozhodovací strom byl vyučen s původními daty.
- Posílený rozhodovací strom byl vyučen pomocí replikovaných dat.

Tato ukázka používá standardní pracovní postup pro datové vědy k vytváření, výukě a testování modelů:

1. Inicializujte algoritmy pro učení pomocí **dvou tříd – vektorový počítač podpory** a se **dvěma třídami se zvyšuje rozhodovací strom**.
1. Použijte **model výuky** pro použití algoritmu pro data a vytvořte skutečný model.
1. Použijte **model skóre** k vytvoření skóre pomocí příkladů testu.

Následující diagram znázorňuje část tohoto kanálu, při které se původní a replikované školicí sady používají ke školení dvou různých SVM modelů. **Model výuky** je připojen ke školicí sadě a **model skóre** je připojen k sadě testů.

![Graf kanálu](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

Ve fázi hodnocení kanálu vypočítáte přesnost každého ze čtyř modelů. Pro tento kanál použijte **vyhodnocený model** a porovnejte příklady, které mají stejné náklady na klasifikaci.

Modul **vyhodnocení modelu** může vypočítat metriky výkonu, a to až pro dva modely skóre. Proto můžete použít jednu instanci **vyhodnocení modelu** k vyhodnocení dvou modelů SVM a jiné instance **vyhodnocení modelu** pro vyhodnocení dvou modelů rozstupného rozhodovacího stromu.

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

Chcete-li zobrazit výsledky kanálu, můžete kliknout pravým tlačítkem myši na výstup vizualizace v modulu poslední **Výběr sloupců v datové sadě** .

![Vizualizovat výstup](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

První sloupec uvádí algoritmus strojového učení, který se používá k vygenerování modelu.

Druhý sloupec indikuje typ sady školení.

Třetí sloupec obsahuje hodnotu přesnosti citlivou na náklady.

Z těchto výsledků vidíte, že je nejlepší přesnost zajištěna modelem vytvořeným se **dvěma třídami pro vektorový stroj podpory** a vyškolenou na replikovanou datovou sadu školení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](how-to-designer-sample-classification-predict-income.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-designer-sample-classification-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)
