---
title: 'Regrese: Předpovědět cenu a porovnat algoritmy'
titleSuffix: Azure Machine Learning service
description: V tomto článku se dozvíte, jak vytvořit experiment složité strojové učení, aniž byste museli napsat jediný řádek kódu pomocí rozhraní visual. Zjistěte, jak trénovat a porovnávání několik modelů regrese předpovídat cenu automobilu podle technické funkce
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c8c813a2304797e71499a916e29c18f8bec2b389
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787800"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Ukázka 2 - regrese: Předpovědět cenu a porovnat algoritmy

Zjistěte, jak vytvořit experiment složité strojové učení, aniž byste museli napsat jediný řádek kódu pomocí rozhraní visual. Tato ukázka trénovat a porovnává několik regresních modelů předpovídat cenu automobilu podle jeho technické funkce. Poskytujeme důvody pro volby provedené v tento experiment tak může vypořádat vlastní strojového učení problémy.

Pokud právě začínáte s machine learningem, může trvat podívat [základní verze](ui-sample-regression-predict-automobile-price-basic.md) tohoto experimentu zobrazíte základní regrese experimentovat.

Tady je dokončené grafu pro tento experiment:

[![Graf experimentu](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro experiment příklad 2:

    ![Otevřete experiment](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Souhrn testu

Tyto kroky použijte k sestavení testu:

1. Získáte data.
1. Předběžně zpracovat data.
1. Trénování modelu.
1. Testování, vyhodnocení a porovnávání vzorů.

## <a name="get-the-data"></a>Získání dat

V tento experiment používáme **Automobile price data (Raw)** datovou sadu, která je z úložiště UCI Machine Learning. Tato datová sada obsahuje 26 sloupce, které obsahují informace o automobilů, včetně Ujistěte se, modelu, ceny, funkce vozidlo (jako je počet cylindrů), MPG a pojištění rizikové skóre. Cílem tohoto experimentu je předpovídat cenu automobilu.

## <a name="pre-process-the-data"></a>Předběžně zpracovat data

Hlavní data přípravné úkoly zahrnují čištění dat, integrace, transformace, snížení a diskretizace nebo kvantizační. Ve vizuální rozhraní se nachází v modulech provádět tyto operace a jiná data předběžného zpracování úkolů v **transformace dat** skupiny na levém panelu.

V tento experiment používáme **výběr sloupců v datové sadě** modulu, který chcete vyloučit normalized-losses, které mají mnoho chybějících hodnot. Potom použijeme **vyčištění chybějících dat** se mají odebrat řádky, které chybí některé hodnoty. To pomáhá čisté vytvoření trénovací data.

![Předběžné zpracování dat](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Trénování modelu

Machine learning problémy se liší. Běžné úkoly strojového učení zahrnují klasifikaci, clustering, regresi a doporučené systémů, z nichž každá může vyžadovat jiný algoritmus. Zvolený algoritmus často závisí na požadavcích případu použití. Jakmile vyberete algoritmus, budete muset vyladění jeho parametrů tak moct trénovat přesnější modelu. Pak budete muset vyhodnotit všechny modely na základě metrik, jako je přesnost, srozumitelnost a efektivitu.

Protože cílem tohoto experimentu je k předvídání cen automobilů a popisek sloupce (cena) obsahuje reálná čísla, regresní model je dobrou volbou. Vzhledem k tomu, že mnoho funkcí je poměrně málo početnému (méně než 100) a nejsou tyto funkce řídký, bude pravděpodobně nelineárních hranici rozhodnutí.

K porovnání výkonu různých algoritmů, používáme dva nelineárních algoritmy **Boosted regrese rozhodovacího stromu** a **rozhodnutí doménové struktury regrese**, sestavovat modely. Algoritmy mají parametry, které můžete změnit, ale použijeme výchozí hodnoty pro tento experiment.

Používáme **rozdělení dat** modulu náhodně rozdělit vstupní data tak, aby trénovací datové sady obsahuje 70 % modulů pro původní data a testování datová sada obsahuje 30 % původní data.

## <a name="test-evaluate-and-compare-the-models"></a>Testování, vyhodnocení a porovnávání vzorů

Používáme dvě různé sady náhodně zvolená datového a natrénuje a otestuje pak modelu, jak je popsáno v předchozí části. Jsme rozdělit datovou sadu a různých datových sad a natrénuje a otestuje model provést vyhodnocení modelu další cíle.

Po model se trénuje, používáme **Score Model** a **Evaluate Model** moduly, které generují předpokládané výsledky a vyhodnocení modelů. **Určení skóre modelu** generuje předpovědi pro datovou sadu testů s použitím trénovaného modelu. Pak předáme skóre k **Evaluate Model** ke generování metrik.

V tento experiment používáme dvě instance **Evaluate Model** porovnat dvě dvojice modelů.

Nejprve jsme porovnat dva algoritmy na trénovací datové sady.
Za druhé jsme porovnat dva algoritmy na testovací datové sadě.
Tady jsou výsledky:

![Porovnejte výsledky](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Tyto výsledky ukazují, že model sestavený s **Boosted regrese rozhodovacího stromu** má nižší kořenové znamenat kvadratická chyba než model sestavený **rozhodnutí doménové struktury regrese**.

Algoritmy mají nižší chyba na trénovací datové sady než nezobrazený testovací datové sady.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte službu k dispozici pro vizuální rozhraní ukázky:

- [Ukázka 1 - regrese: Předpovídat cenu automobilu představuje jeden](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 3 – klasifikace: Předpovědět úvěrové riziko](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď výpovědi](ui-sample-classification-predict-churn.md)
