---
title: 'Návrhář: příklad prediktivních cen aut (rozšířené)'
titleSuffix: Azure Machine Learning
description: Sestavujte & Porovnejte více modelů ML regrese a předpovídat cenu automobilu na základě technických funkcí v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a4148389992bc100be66175a75b32d61acaf2f46
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659865"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Školení & Porovnejte více regresních modelů a předpovídat ceny automobilů pomocí návrháře Azure Machine Learning

**Návrhář (Preview) – ukázka 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Naučte se vytvářet kanály strojového učení bez psaní jediného řádku kódu pomocí návrháře (Preview). Tato ukázka vlaků a porovnává více regresních modelů a předpovídá cenu auta na základě svých technických funkcí. Nabídneme vám odůvodnění pro volby provedené v tomto kanálu, abyste se mohli vypořádat s vlastními problémy machine learningu.

Pokud se strojové učení teprve začíná, podívejte se na [základní verzi](service/how-to-designer-sample-regression-automobile-price-basic.md) tohoto kanálu.

Zde je dokončený graf pro tento kanál:

[![Graf kanálu](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknutím na položku ukázka 2 ji otevřete. 

## <a name="pipeline-summary"></a>Souhrn kanálu

Pomocí následujících kroků Sestavte kanál strojového učení:

1. Získat data.
1. Předběžné zpracování dat.
1. Proveďte výuku modelu.
1. Otestujte, vyhodnoťte a porovnejte modely.

## <a name="get-the-data"></a>Získání dat

Tato ukázka používá datovou sadu **dat automobil (RAW)** , která je z úložiště UCI Machine Learning. Tato datová sada obsahuje 26 sloupců, které obsahují informace o Automobiles, včetně funkcí make, model, Price, funkcí vozidel (jako je počet lahví), MPG a hodnocení rizikového rizika.

## <a name="pre-process-the-data"></a>Předběžné zpracování dat

Hlavní úkoly přípravy dat zahrnují čištění dat, integraci, transformaci, snížení a nalezených diskretizační nebo kvantizační. V Návrháři můžete najít moduly, které provádějí tyto operace a další úlohy předběžného zpracování dat ve skupině **transformace dat** na levém panelu.

Použijte modul **Výběr sloupců v datové sadě** k vyloučení normalizovaných ztrát, které mají mnoho chybějících hodnot. Pak pomocí **Vyčištění chybějících dat** odeberete řádky, které obsahují chybějící hodnoty. To pomáhá vytvořit čistou sadu školicích dat.

![Předběžné zpracování dat](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Trénování modelu

Problémy strojového učení se liší. Mezi běžné úkoly strojového učení patří klasifikace, clusteringu, regrese a systémy doporučení, z nichž každá může vyžadovat jiný algoritmus. Vaše volba algoritmu často závisí na požadavcích případu použití. Po výběru algoritmu je třeba vyladit jeho parametry a naučit se model lépe přesný. Pak je potřeba vyhodnotit všechny modely na základě metrik, jako je přesnost, intelligibility a efektivita.

Vzhledem k tomu, že cílem tohoto kanálu je předpovědět ceny automobilu a protože sloupec popisku (price) obsahuje skutečná čísla, je regresní model vhodný. Vzhledem k tomu, že počet funkcí je relativně malý (méně než 100) a tyto funkce nejsou zhuštěné, je pravděpodobně nelineární hranice rozhodnutí.

Pro porovnání výkonu různých algoritmů používáme dva nelineární algoritmy, zvýšili jsme regresi **rozhodovacího stromu** a **regresi rozhodovací doménové struktury**a sestavíte modely. Oba algoritmy mají parametry, které lze změnit, ale tato ukázka používá výchozí hodnoty pro tento kanál.

Použijte modul **rozdělit data** k náhodnému rozdělení vstupních dat tak, aby datová sada školení obsahovala 70% původních dat a testovací datová sada obsahuje 30% původních dat.

## <a name="test-evaluate-and-compare-the-models"></a>Testování, vyhodnocení a porovnání modelů

Použijete dvě různé sady náhodně zvolených dat ke školení a otestování modelu, jak je popsáno v předchozí části. Rozdělte datovou sadu a použijte různé datové sady ke školení a testování modelu, aby bylo vyhodnocení modelu více objektivně.

Po vyzkoušení modelu použijte **model skóre** a **vyhodnoťte moduly modelů** pro generování předpokládaných výsledků a vyhodnocení modelů. **Model skóre** generuje předpovědi pro testovací datovou sadu pomocí trained model. Pak předejte skóre k **vyhodnocení modelu** pro generování metrik vyhodnocení.



Tady jsou výsledky:

![Porovnat výsledky](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

Tyto výsledky ukazují, že model sestavený s **regresí pro zvýšení rozhodovacího stromu** má nižší hlavní střední chybu než model sestavený na **regresi rozhodovací doménové struktury**.



## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:

- [Ukázka 1 – regrese: předpověď ceny automobilu](service/how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](service/how-to-designer-sample-classification-predict-income.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](service/how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](service/how-to-designer-sample-classification-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](service/how-to-designer-sample-classification-flight-delay.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)
