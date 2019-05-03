---
title: 'Regrese: Předpovědět cenu'
titleSuffix: Azure Machine Learning service
description: Tento ukázkový experiment vizuální rozhraní ukazuje, jak sestavit regresní model pro předpověď ceny automobilu představuje jeden. Proces zahrnuje trénování, testování a vyhodnocení modelů na datové sadě Automobile price data (Raw).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: fa9b9179cda767d69d08dcd357a03123bde901cb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028887"
---
# <a name="sample-1---regression-predict-price"></a>Ukázka 1 - regrese: Předpovědět cenu

Tento ukázkový experiment vizuální rozhraní ukazuje, jak sestavit regresní model pro předpověď ceny automobilu představuje jeden. Proces zahrnuje trénování, testování a vyhodnocení modelu s použitím **Automobile price data (Raw)** datové sady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro experiment Příklad 1:

    ![Otevřete experiment](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="related-sample"></a>Související ukázkové

[Ukázka 2 - regrese: Predikce ceny automobilů (porovnání algoritmy)](ui-sample-regression-predict-automobile-price-compare-algorithms.md) poskytuje mnohem komplikovanější ukázkový experiment, který byl problém vyřešen stejné jako tento experiment s využitím dvou různých regresních modelů. Ukazuje, jak rychle porovnat různé algoritmy. Pokud hledáte pokročilejší ukázku, projděte si ho.

## <a name="experiment-summary"></a>Souhrn testu

Tyto kroky použijte k sestavení testu:

1. Získáte data.
1. Předběžně zpracovat data.
1. Trénování modelu.
1. Testování, vyhodnocení a porovnávání vzorů.

Tady je úplný graf testu:

![Graf experimentu](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="get-the-data"></a>Získání dat

V tento experiment používáme **Automobile price data (Raw)** datovou sadu, která je z úložiště UCI Machine Learning. Datová sada obsahuje 26 sloupce, které obsahují informace o automobilů, včetně Ujistěte se, modelu, ceny, funkce vozidlo (jako je počet cylindrů), MPG a pojištění rizikové skóre. Cílem tohoto experimentu je předpovídat cenu automobilu.

## <a name="pre-process-the-data"></a>Předběžně zpracovat data

Hlavní data přípravné úkoly zahrnují čištění dat, integrace, transformace, snížení a diskretizace nebo kvantizační. Ve vizuální rozhraní se nachází v modulech provádět tyto operace a jiná data předběžného zpracování úkolů v **transformace dat** skupiny na levém panelu.

Používáme **výběr sloupců v datové sadě** modulu, který chcete vyloučit normalized-losses, které mají mnoho chybějících hodnot. Potom použijeme **vyčištění chybějících dat** se mají odebrat řádky, které chybí některé hodnoty. To pomáhá čisté vytvoření trénovací data.

![Předběžné zpracování dat](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Trénování modelu
Machine learning problémy se liší. Běžné úkoly strojového učení zahrnují klasifikaci, clustering, regresi a doporučené systémů, z nichž každá může vyžadovat jiný algoritmus. Zvolený algoritmus často závisí na požadavcích případu použití. Jakmile vyberete algoritmus, budete muset vyladění jeho parametrů tak moct trénovat přesnější modelu. Pak budete muset vyhodnotit všechny modely na základě metrik, jako je přesnost, srozumitelnost a efektivitu.

Protože cílem tohoto experimentu je k předvídání cen automobilů a popisek sloupce (cena) obsahuje reálná čísla, regresní model je dobrou volbou. Vzhledem k tomu, že mnoho funkcí je poměrně málo početnému (méně než 100) a nejsou tyto funkce řídký, bude pravděpodobně nelineárních hranici rozhodnutí. Takže použijeme **rozhodnutí doménové struktury regrese** pro tento experiment.

Používáme **rozdělení dat** modulu náhodně rozdělit vstupní data tak, aby trénovací datové sady obsahuje 70 % modulů pro původní data a testování datová sada obsahuje 30 % původní data.

## <a name="test-evaluate-and-compare"></a>Otestovat, vyhodnocení a porovnat

 Jsme rozdělit datovou sadu a různých datových sad a natrénuje a otestuje model provést vyhodnocení modelu další cíle.

Po model se trénuje, používáme **Score Model** a **Evaluate Model** moduly, které generují předpokládané výsledky a vyhodnocení modelů.

**Určení skóre modelu** generuje předpovědi pro datovou sadu testů s použitím trénovaného modelu. Pokud chcete zkontrolovat výsledek, vyberte na výstupní port modulu **Score Model** a pak vyberte **vizualizovat**.

![Výsledek skóre](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Pak předáme skóre, které chcete **Evaluate Model** modul pro generování metrik. Pokud chcete zkontrolovat výsledek, vyberte na výstupní port modulu **Evaluate Model** a pak vyberte **vizualizovat**.

![Výsledek vyhodnocení](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte službu k dispozici pro vizuální rozhraní ukázky:

- [Ukázka 2 - regrese: Porovnání algoritmy pro předpověď cen automobilů](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpovědět úvěrové riziko](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď výpovědi](ui-sample-classification-predict-churn.md)
