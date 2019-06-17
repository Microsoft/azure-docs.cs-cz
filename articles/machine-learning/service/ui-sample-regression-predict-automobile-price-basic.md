---
title: 'Regrese: Predikce ceny'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak sestavit služby machine learning model pro předpověď ceny automobilu představuje jeden aniž byste museli napsat jediný řádek kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787845"
---
# <a name="sample-1---regression-predict-price"></a>Ukázka 1 - regrese: Predikce ceny

Zjistěte, jak sestavit model strojového učení regrese aniž byste museli napsat jediný řádek kódu pomocí vizuální rozhraní.

Tento experiment železniční **rozhodnutí regresor doménové struktury** předpovědět automobilu je cena závisí na technických prvků, modelu, výkon a velikost. Protože ale My se snažíme odpověď na otázku "Kolik?" tomu se říká regresní problém. Můžete však použít stejný základní postup v tomto experimentu libovolného typu machine learning problém řešit, ať to regrese, klasifikace, clustering a tak dále.

Jsou základní kroky školení model strojového učení:

1. Získání dat
1. Předběžně zpracovat data
1. Trénování modelu
1. Vyhodnocení modelu

Tady je graf konečné, dokončení experimentu, který budeme pracovat na. Poskytujeme důvody pro všechny moduly, abyste měli podobné rozhodnutí, která sami.

![Graf experimentu](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro experiment Příklad 1:

    ![Otevřete experiment](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

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