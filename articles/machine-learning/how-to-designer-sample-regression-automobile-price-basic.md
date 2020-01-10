---
title: 'Návrhář: příklad předpovídá ceny aut (Basic)'
titleSuffix: Azure Machine Learning
description: Sestavte regresní model ML pro předpověď ceny automobilu, aniž byste museli psát jediný řádek kódu pomocí návrháře Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 156b963fc1644d1f863d8ddd1d86c15b311e18a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763381"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Předpověď cen automobilů pomocí Azure Machine Learning designeru pomocí regrese

**Návrhář (Preview) – ukázka 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Naučte se vytvářet regresní model Machine Learning bez nutnosti psát jediný řádek kódu pomocí návrháře (Preview).

Tento kanál nakládá **lineární regresorou** předpověď ceny automobilu na základě technických funkcí, jako je značka, model, aut a velikost. Vzhledem k tomu, že se snažíte odpovědět na otázku "kolik věcí"? Tato chyba se nazývá regresní problém. V tomto příkladu ale můžete použít stejné základní kroky, abyste mohli řešit jakýkoli typ problému strojového učení bez ohledu na to, jestli jde o regresi, klasifikaci, clusteringu a tak dále.

Základní kroky pro školicí model strojového učení jsou:

1. Získání dat
1. Předběžné zpracování dat
1. Trénování modelu
1. Vyhodnocení modelu

Tady je poslední dokončený Graf kanálu. Tento článek představuje racionální pro všechny moduly, abyste mohli dělat podobná rozhodnutí sami.

![Graf kanálu](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknutím na ukázku 1 Otevřete IT Marketplace.


## <a name="get-the-data"></a>Získání dat

Tato ukázka používá datovou sadu **dat automobil (RAW)** , která je z úložiště UCI Machine Learning. Datová sada obsahuje 26 sloupců, které obsahují informace o Automobiles, včetně funkcí make, model, Price, funkcí pro vozidlo (jako je počet lahví), MPG a hodnocení rizikového rizika. Cílem této ukázky je předpovědět cenu auta.

## <a name="pre-process-the-data"></a>Předběžné zpracování dat

Hlavní úkoly přípravy dat zahrnují čištění dat, integraci, transformaci, snížení a nalezených diskretizační nebo kvantizační. V Návrháři můžete najít moduly, které provádějí tyto operace a další úlohy předběžného zpracování dat ve skupině **transformace dat** na levém panelu.

Použijte modul **Výběr sloupců v datové sadě** k vyloučení normalizovaných ztrát, které mají mnoho chybějících hodnot. Pak pomocí možnosti **vyčistit chybějící data** odeberte řádky, které obsahují chybějící hodnoty. To pomáhá vytvořit čistou sadu školicích dat.

![Předběžné zpracování dat](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Trénování modelu

Problémy strojového učení se liší. Mezi běžné úkoly strojového učení patří klasifikace, clusteringu, regrese a systémy doporučení, z nichž každá může vyžadovat jiný algoritmus. Vaše volba algoritmu často závisí na požadavcích případu použití. Po výběru algoritmu je třeba vyladit jeho parametry a naučit se model lépe přesný. Pak je potřeba vyhodnotit všechny modely na základě metrik, jako je přesnost, intelligibility a efektivita.

Vzhledem k tomu, že cílem této ukázky je předpovědět ceny automobilu a protože sloupec popisku (price) obsahuje skutečná čísla, je regresní model dobrou volbou. Vzhledem k tomu, že počet funkcí je relativně malý (méně než 100) a tyto funkce nejsou zhuštěné, je pravděpodobně nelineární hranice rozhodnutí. Proto používáme pro tento kanál **regresi rozhodovací doménové struktury** .

Použijte modul **rozdělit data** k náhodnému rozdělení vstupních dat tak, aby datová sada školení obsahovala 70% původních dat a testovací datová sada obsahuje 30% původních dat.

## <a name="test-evaluate-and-compare"></a>Testování, vyhodnocení a porovnání

Rozdělte datovou sadu a použijte různé datové sady ke školení a testování modelu, aby bylo vyhodnocení modelu více objektivně.

Po vyzkoušení modelu můžete k vygenerování předpokládaných výsledků a vyhodnocení modelů použít **model skóre** a **vyhodnotit moduly modelu** .

**Model skóre** generuje předpovědi pro testovací datovou sadu pomocí trained model. Pro kontrolu výsledku vyberte výstupní port **modelu skóre** a pak vyberte **vizualizovat**.

![Výsledek skóre](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Předání výsledků do modulu **vyhodnocení modelu** za účelem generování metrik vyhodnocení. Chcete-li zjistit výsledek, vyberte výstupní port **modelu vyhodnocení** a pak vyberte **vizualizovat**.

![Vyhodnotit výsledek](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:

- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](how-to-designer-sample-classification-predict-income.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-designer-sample-classification-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)