---
title: 'Příklad návrháře #6: klasifikace pro předpověď zpoždění letů'
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte, jak vytvořit model strojového učení pro předpověď zpoždění letu pomocí návrháře přetahování a vlastního kódu R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 4b21ebffe8959809acc71fc09aff7e58873e10b8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515522"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Ukázka 6 – klasifikace: předpověď zpoždění letu pomocí R
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Tento kanál používá historické údaje o letu a počasí k předběžnému předběžnému zpoždění, pokud se naplánovaný osobní let zpozdí o více než 15 minut. K tomuto problému může dojít jako problém klasifikace, předpověď dvou tříd: opožděné nebo včas.

Toto je konečný Graf kanálu pro tuto ukázku:

[![Graf kanálu](media/how-to-ui-sample-classification-predict-flight-delay/pipeline-graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Kliknutím na Ukázka 6 otevřete.

## <a name="get-the-data"></a>Získání dat

Tato ukázka používá datovou sadu **dat o zpoždění letů** . Je součástí shromažďování dat TranStats z ministerstva dopravy USA. Datová sada obsahuje informace o zpoždění letu od dubna do října 2013. Datová sada byla předem zpracována následujícím způsobem:

* Filtrováno tak, aby zahrnovalo letiště 70 nejvytíženější do kontinentální USA.
* Zrušené lety přeoznačené jako zpožděné o více než 15 minut.
* Vyfiltrováno odcházející lety.
* Vybráno 14 sloupců.

Pro doplnění letových dat se použije **datová sada počasí** . Data o počasí obsahují hodinu a počasí na základě půdy z NOAA a představují pozorování z povětrnostních stanic na letišti, které pokrývají stejné časové období jako datová sada letů. Byl předem zpracován následujícím způsobem:

* ID povětrnostních stanic byly namapovány na odpovídající ID letišť.
* Některé z povětrnostních stanic, které nejsou spojené s 70 nejvytíženější letiště, se odebraly.
* Sloupec data byl rozdělen na samostatné sloupce: rok, měsíc a den.
* Vybráno 26 sloupců.

## <a name="pre-process-the-data"></a>Předběžné zpracování dat

Datová sada obvykle vyžaduje před analýzou některé předběžné zpracování.

![zpracování dat](media/how-to-ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Letová data

Sloupce **přepravce**, **OriginAirportID**a **DestAirportID** se ukládají jako celá čísla. Jsou však kategorií atributy, a to pomocí modulu **Upravit metadata** a převeďte je na kategorií.

![Upravit metadata](media/how-to-ui-sample-classification-predict-flight-delay/edit-metadata.png)

Pak použijte modul **Výběr sloupců** v datové sadě k vyloučení ze sloupců datové sady, které jsou potenciálními nevrácenými cíli: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **year**. 

Pokud se chcete připojit ke letovým záznamům pomocí hodinových záznamů počasí, použijte naplánovaný čas odchodu jako jeden z klíčů JOIN. Aby bylo možné spojení provést, musí být sloupec CSRDepTime zaokrouhlený dolů na nejbližší hodinu, kterou provádí v modulu **spuštění skriptu jazyka R** . 

### <a name="weather-data"></a>Data o počasí

Sloupce, které mají velký podíl chybějících hodnot, jsou vyloučeny pomocí modulu **projektové sloupce** . Mezi tyto sloupce patří všechny sloupce s hodnotou řetězce: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**a **StationPressure.** .

Na zbývajících sloupcích se pak použije modul **Vyčištění chybějících dat** , aby se odstranily řádky s chybějícími daty.

Doby pozorování počasí se zaokrouhlují na nejbližší celou hodinu. Plánované lety a doby pozorování počasí jsou v opačném směru zaokrouhleny, aby model používaly pouze počasí před letovým časem. 

Vzhledem k tomu, že údaje o počasí jsou hlášeny v místním čase, účtují se rozdíly v časovém pásmu pro odečtením sloupců časového pásma od naplánovaného času odchodu a doby pozorování počasí. Tyto operace se provádějí pomocí modulu **skriptu pro spuštění R** .

### <a name="joining-datasets"></a>Spojování datových sad

Letové záznamy jsou spojeny s daty o počasí v počátku letu (**OriginAirportID**) pomocí modulu **Join data** Module.

 ![spojit lety a počasí podle původu](media/how-to-ui-sample-classification-predict-flight-delay/join-origin.png)


Letové záznamy jsou spojené s daty o počasí, a to s využitím cíle letu (**DestAirportID**).

 ![Připojení letu a počasí podle cíle](media/how-to-ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Příprava ukázek školení a testování

Modul **rozdělit data** rozdělí data do dne do září záznamů pro školení a pro test na základě záznamů.

 ![Rozdělení dat školení a testování](media/how-to-ui-sample-classification-predict-flight-delay/split.png)

Sloupce rok, měsíc a časové pásmo jsou z datové sady školení odebrány pomocí modulu vybrat sloupce.

## <a name="define-features"></a>Definovat funkce

Ve strojovém učení jsou funkce jednotlivé měřitelné vlastnosti něčeho, co vás zajímá. Nalezení silné sady funkcí vyžaduje experimenty a znalosti v doméně. Některé příznaky jsou pro predikci cíle vhodnější než jiné. Některé funkce také mohou mít silnou korelaci s jinými funkcemi a nebudou do modelu přidávat nové informace. Tyto funkce je možné odebrat.

Chcete-li vytvořit model, můžete použít všechny dostupné funkce nebo vybrat podmnožinu funkcí.

## <a name="choose-and-apply-a-learning-algorithm"></a>Volba a použití algoritmu učení

Vytvořte model pomocí modulu **logistické regrese dvou tříd** a prohlaste ho v datové sadě školení. 

Výsledkem modulu **vlakového modelu** je vyškolený model klasifikace, který se dá použít ke stanovení skóre nových vzorků, aby bylo možné předpovědiovat. Pomocí sady testů můžete vygenerovat skóre z vycvičených modelů. Pak pomocí modulu **vyhodnocení modelu** Analyzujte a porovnejte kvalitu modelů.
kanál po spuštění kanálu můžete zobrazit výstup z modulu **skóre modelu** tak, že kliknete na výstupní port a vyberete **vizualizovat**. Výstup obsahuje popisky s skóre a pravděpodobnosti pro popisky.

Nakonec můžete testovat kvalitu výsledků, přidat modul **vyhodnocení modelu** na plátno kanálu a propojit levý vstupní port s výstupem modulu určení skóre modelu. Spusťte kanál a zobrazte výstup modulu **vyhodnocení modelu** tak, že kliknete na výstupní port a vyberete **vizualizovat**.

## <a name="evaluate"></a>Vyhodnocení
Model logistické regrese má v sadě testů AUC 0,631.

 ![Vyhodnocení](media/how-to-ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Další kroky

Prozkoumejte další ukázky, které jsou k dispozici pro návrháře:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](how-to-designer-sample-classification-predict-income.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-designer-sample-classification-churn.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)