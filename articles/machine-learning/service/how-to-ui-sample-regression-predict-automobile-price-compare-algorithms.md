---
title: 'Příklad vizuálního rozhraní #3: regrese na ceny a porovnávání algoritmů'
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte, jak vytvořit složitý experiment strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní. Naučte se, jak vytvořit a porovnat více regresních modelů a předpovídat cenu automobilu na základě technických funkcí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c40d76b87ca7437e25c567176b0309f08f3ca9f2
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131568"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Ukázka 2 – regrese: Předpověď cen a porovnávání algoritmů

Naučte se vytvářet složitý experiment strojového učení bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní. Tato ukázka vlaků a porovnává více regresních modelů a předpovídá cenu auta na základě svých technických funkcí. Nabídneme vám odůvodnění pro volby, které jsme udělali v tomto experimentu, abyste mohli řešit vlastní problémy machine learningu.

Pokud se strojové učení teprve začíná, podívejte se na [základní verzi](how-to-ui-sample-regression-predict-automobile-price-basic.md) tohoto experimentu.

Zde je dokončený graf pro tento experiment:

[![Graf experimentu](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro experiment Sample 2:

    ![Otevřít experiment](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Shrnutí experimentů

Pomocí následujících kroků sestavíte experiment ve službě Machine Learning:

1. Získat data.
1. Předběžné zpracování dat.
1. Proveďte výuku modelu.
1. Otestujte, vyhodnoťte a porovnejte modely.

## <a name="get-the-data"></a>Získání dat

Tato ukázka používá datovou sadu **dat automobil (RAW)** , která je z úložiště UCI Machine Learning. Tato datová sada obsahuje 26 sloupců, které obsahují informace o Automobiles, včetně funkcí make, model, Price, funkcí vozidel (jako je počet lahví), MPG a hodnocení rizikového rizika.

## <a name="pre-process-the-data"></a>Předběžné zpracování dat

Hlavní úkoly přípravy dat zahrnují čištění dat, integraci, transformaci, snížení a nalezených diskretizační nebo kvantizační. V rozhraní Visual Interface můžete najít moduly, které provádějí tyto operace a další úlohy předběžného zpracování dat ve skupině **transformace dat** na levém panelu.

Použijte modul **Výběr sloupců v datové sadě** k vyloučení normalizovaných ztrát, které mají mnoho chybějících hodnot. Pak pomocí **Vyčištění chybějících dat** odeberete řádky, které obsahují chybějící hodnoty. To pomáhá vytvořit čistou sadu školicích dat.

![Předběžné zpracování dat](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Trénování modelu

Problémy strojového učení se liší. Mezi běžné úkoly strojového učení patří klasifikace, clusteringu, regrese a systémy doporučení, z nichž každá může vyžadovat jiný algoritmus. Vaše volba algoritmu často závisí na požadavcích případu použití. Po výběru algoritmu je třeba vyladit jeho parametry a naučit se model lépe přesný. Pak je potřeba vyhodnotit všechny modely na základě metrik, jako je přesnost, intelligibility a efektivita.

Vzhledem k tomu, že cílem tohoto experimentu je předpovědět ceny automobilu a protože sloupec popisku (price) obsahuje skutečná čísla, je regresní model dobrou volbou. Vzhledem k tomu, že počet funkcí je relativně malý (méně než 100) a tyto funkce nejsou zhuštěné, je pravděpodobně nelineární hranice rozhodnutí.

Vzhledem k tomu, že cílem tohoto experimentu je předpovědět ceny automobilu a protože sloupec popisku (price) obsahuje skutečná čísla, je regresní model dobrou volbou. Vzhledem k tomu, že počet funkcí je relativně malý (méně než 100) a tyto funkce nejsou zhuštěné, je pravděpodobně nelineární hranice rozhodnutí.

Pro porovnání výkonu různých algoritmů používáme dva nelineární algoritmy, zvýšili jsme regresi **rozhodovacího stromu** a **regresi rozhodovací doménové struktury**a sestavíte modely. Oba algoritmy mají parametry, které lze změnit, ale tato ukázka používá výchozí hodnoty pro tento experiment.

Použijte modul **rozdělit data** k náhodnému rozdělení vstupních dat tak, aby datová sada školení obsahovala 70% původních dat a testovací datová sada obsahuje 30% původních dat.

## <a name="test-evaluate-and-compare-the-models"></a>Testování, vyhodnocení a porovnání modelů

Použijete dvě různé sady náhodně zvolených dat ke školení a otestování modelu, jak je popsáno v předchozí části. Rozdělte datovou sadu a použijte různé datové sady ke školení a testování modelu, aby bylo vyhodnocení modelu více objektivně.

Po vyzkoušení modelu použijte **model skóre** a **vyhodnoťte moduly modelů** pro generování předpokládaných výsledků a vyhodnocení modelů. **Model skóre** generuje předpovědi pro testovací datovou sadu pomocí trained model. Pak předejte skóre k **vyhodnocení modelu** pro generování metrik vyhodnocení.

V tomto experimentu použijete dvě instance **vyhodnocení modelu** k porovnání dvou párů modelů.

Nejprve Porovnejte dva algoritmy v datové sadě školení.
Za druhé Porovnejte dva algoritmy pro testovací datovou sadu.

Tady jsou výsledky:

![Porovnat výsledky](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Tyto výsledky ukazují, že model sestavený s **regresí pro zvýšení rozhodovacího stromu** má nižší hlavní střední chybu než model sestavený na **regresi rozhodovací doménové struktury**.

Oba algoritmy mají v datové sadě školení nižší chybu než v datové sadě nepřesných testů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: Předpověď ceny automobilu](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 3 – klasifikace: Předpověď úvěrového rizika](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď změn](how-to-ui-sample-classification-predict-churn.md)
- [Ukázka 6 – klasifikace: Předpověď zpoždění letů](how-to-ui-sample-classification-predict-flight-delay.md)
