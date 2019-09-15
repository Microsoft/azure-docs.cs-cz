---
title: 'Příklad vizuálního rozhraní #1: regrese pro předpověď ceny'
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet model strojového učení pro předpověď ceny automobilu bez nutnosti psát jediný řádek kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 4d41efcaeb93c0843686f9e8ef1b62da5367e071
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996541"
---
# <a name="sample-1---regression-predict-price"></a>Ukázka 1 – regrese: Predikce ceny

Naučte se, jak vytvořit regresní model Machine Learning bez nutnosti psát jediný řádek kódu pomocí vizuálního rozhraní.

Tento experiment nakládá s **rozhodovací doménovou strukturou regresor** , aby předpovídat cenu automobilu na základě technických funkcí, jako je značka, model, aut a velikost. Vzhledem k tomu, že se snažíme odpovědět na otázku "kolik věcí?" Tato chyba se nazývá regresní problém. V tomto experimentu ale můžete použít stejné základní kroky, abyste mohli řešit jakýkoli typ problému strojového učení, ať už jde o regresi, klasifikaci, clusteringu a tak dále.

Základní kroky pro školicí model strojového učení jsou:

1. Získání dat
1. Předběžné zpracování dat
1. Trénování modelu
1. Vyhodnocení modelu

Tady je poslední dokončený graf experimentu, na kterém budeme pracovat. Pro všechny moduly poskytneme odůvodnění, abyste mohli dělat podobná rozhodnutí sami.

![Graf experimentu](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro zkoušku Sample 1:

    ![Otevřít experiment](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Získání dat

V tomto experimentu používáme datovou sadu **dat automobil (RAW)** , která je z úložiště UCI Machine Learning. Datová sada obsahuje 26 sloupců, které obsahují informace o Automobiles, včetně funkcí make, model, Price, funkcí pro vozidlo (jako je počet lahví), MPG a hodnocení rizikového rizika. Cílem tohoto experimentu je předpovědět cenu auta.

## <a name="pre-process-the-data"></a>Předběžné zpracování dat

Hlavní úkoly přípravy dat zahrnují čištění dat, integraci, transformaci, snížení a nalezených diskretizační nebo kvantizační. Ve vizuálním rozhraní můžete najít moduly, které provádějí tyto operace a další úlohy předběžného zpracování dat ve skupině pro **transformaci dat** na levém panelu.

Modul **Výběr sloupců v datové sadě** používáme k vyloučení normalizovaných ztrát, které mají mnoho chybějících hodnot. Pak pomocí **Vyčištění chybějících dat** odeberete řádky, které obsahují chybějící hodnoty. To pomáhá vytvořit čistou sadu školicích dat.

![Předběžné zpracování dat](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Trénování modelu

Problémy strojového učení se liší. Mezi běžné úkoly strojového učení patří klasifikace, clusteringu, regrese a systémy doporučení, z nichž každá může vyžadovat jiný algoritmus. Vaše volba algoritmu často závisí na požadavcích případu použití. Po výběru algoritmu je třeba vyladit jeho parametry a naučit se model lépe přesný. Pak je potřeba vyhodnotit všechny modely na základě metrik, jako je přesnost, intelligibility a efektivita.

Vzhledem k tomu, že cílem tohoto experimentu je předpovědět ceny automobilu a protože sloupec popisku (price) obsahuje skutečná čísla, je regresní model dobrou volbou. Vzhledem k tomu, že počet funkcí je relativně malý (méně než 100) a tyto funkce nejsou zhuštěné, je pravděpodobně nelineární hranice rozhodnutí. Proto pro tento experiment používáme **regresi rozhodovací doménové struktury** .

Modul **rozdělit data** používáme k náhodnému rozdělení vstupních dat, aby datová sada školení obsahovala 70% původních dat a testovací datová sada obsahuje 30% původních dat.

## <a name="test-evaluate-and-compare"></a>Testování, vyhodnocení a porovnání

 Datovou sadu rozdělíme a použijeme různé datové sady ke školení a testování modelu, aby bylo vyhodnocení modelu více objektivně.

Po vyzkoušení modelu používáme **model skóre** a **vyhodnocujeme moduly modelů** pro generování předpokládaných výsledků a vyhodnocení modelů.

**Model skóre** generuje předpovědi pro testovací datovou sadu pomocí trained model. Pro kontrolu výsledku vyberte výstupní port **modelu skóre** a pak vyberte **vizualizovat**.

![Výsledek skóre](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Pak předáte skóre do modulu **vyhodnocení modelu** za účelem generování metrik vyhodnocení. Chcete-li zjistit výsledek, vyberte výstupní port **modelu vyhodnocení** a pak vyberte **vizualizovat**.

![Vyhodnotit výsledek](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 2 – regrese: Porovnat algoritmy pro předpověď cen automobilu](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpověď úvěrového rizika](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď změn](ui-sample-classification-predict-churn.md)
- [Ukázka 6 – klasifikace: Předpověď zpoždění letů](ui-sample-classification-predict-flight-delay.md)
