---
title: 'Ukázka vizuálního rozhraní #6: Klasifikace pro předpověď zpoždění letů'
titleSuffix: Azure Machine Learning service
description: V tomto článku se dozvíte, jak vytvořit model strojového učení pro předpověď zpoždění letu pomocí vizuálního rozhraní přetažení a vlastního kódu jazyka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 4c0a990ae3f45fc7b08c157f180d8ecf805c24e6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990027"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Ukázka 6 – klasifikace: Předpověď zpoždění letu pomocí R

Tento experiment používá historická data o letu a počasí k předpovídání, jestli se naplánovaný osobní let zpozdí o více než 15 minut.

K tomuto problému může dojít jako problém klasifikace, předpověď dvou tříd – opožděné nebo v čase. Pokud chcete vytvořit klasifikátor, tento model využívá velký počet příkladů z historických letových dat.

Tady je výsledný graf experimentu:

[![Graf experimentu](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte tlačítko **otevřít** pro experiment s ukázkou 6:

    ![Otevřít experiment](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Získání dat

Tento experiment používá datovou sadu **dat o zpoždění letů** . Je součástí shromažďování dat TranStats z USA. Oddělení dopravy. Datová sada obsahuje informace o zpoždění letu od dubna do října 2013. Před odesláním dat do vizuálního rozhraní byl předem zpracován následujícím způsobem:

* Filtrováno tak, aby zahrnovalo letiště 70 nejvytíženější do kontinentální USA.
* U zrušených letů přepište za zpožděné o více než 15 minut.
* Vyfiltrováno odcházející lety.
* Vybráno 14 sloupců.

Pro doplnění letových dat se použije **datová sada počasí** . Data o počasí obsahují hodinové počasí založené na půdě z NOAA a představují pozorování z povětrnostních stanic na letišti, které pokrývají stejné časové období v dubnu-říjnu 2013. Před odesláním do vizuálního rozhraní Azure ML byl předem zpracován následujícím způsobem:

* ID povětrnostních stanic byly namapovány na odpovídající ID letišť.
* Některé z povětrnostních stanic, které nejsou spojené s 70 nejvytíženější letiště, se odebraly.
* Sloupec data byl rozdělen do samostatných sloupců: Rok, měsíc a den.
* Vybráno 26 sloupců.

## <a name="pre-process-the-data"></a>Předběžné zpracování dat

Datová sada obvykle vyžaduje před analýzou některé předběžné zpracování.

![zpracování dat](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Letová data

Sloupce **přepravce**, **OriginAirportID**a **DestAirportID** se ukládají jako celá čísla. Jsou však kategorií atributy, a to pomocí modulu **Upravit metadata** a převeďte je na kategorií.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Pak použijte modul **Vybrat sloupce** v datové sadě, který se vyloučí ze sloupců datové sady, které jsou možné nevrácením cíle: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **year**. 

Pokud se chcete připojit ke letovým záznamům pomocí hodinových záznamů počasí, použijte naplánovaný čas odchodu jako jeden z klíčů JOIN. Aby bylo možné spojení provést, musí být sloupec CSRDepTime zaokrouhlený dolů na nejbližší hodinu, kterou provádí v modulu **spuštění skriptu jazyka R** . 

### <a name="weather-data"></a>Data o počasí

Sloupce, které mají velký podíl chybějících hodnot, jsou vyloučeny pomocí modulu **projektové sloupce** . Tyto sloupce obsahují všechny sloupce s hodnotou řetězce: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**a **StationPressure**.

Na zbývajících sloupcích se pak použije modul **Vyčištění chybějících dat** , aby se odstranily řádky s chybějícími daty.

Doby pozorování počasí se zaokrouhlují na nejbližší celou hodinu. Plánované lety a doby pozorování počasí jsou v opačném směru zaokrouhleny, aby model používaly pouze počasí před letovým časem. 

Vzhledem k tomu, že údaje o počasí jsou hlášeny v místním čase, účtují se rozdíly v časovém pásmu pro odečtením sloupců časového pásma od naplánovaného času odchodu a doby pozorování počasí. Tyto operace se provádějí pomocí modulu **skriptu pro spuštění R** .

### <a name="joining-datasets"></a>Spojování datových sad

Letové záznamy jsou spojeny s daty o počasí v počátku letu (**OriginAirportID**) pomocí modulu **Join data** Module.

 ![spojit lety a počasí podle původu](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Letové záznamy jsou spojené s daty o počasí, a to s využitím cíle letu (**DestAirportID**).

 ![Připojení letu a počasí podle cíle](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Příprava ukázek školení a testování

Modul **rozdělit data** rozdělí data do dne do září záznamů pro školení a pro test na základě záznamů.

 ![Rozdělení dat školení a testování](media/ui-sample-classification-predict-flight-delay/split.png)

Sloupce rok, měsíc a časové pásmo jsou z datové sady školení odebrány pomocí modulu vybrat sloupce.

## <a name="define-features"></a>Definovat funkce

Ve strojovém učení jsou funkce jednotlivé měřitelné vlastnosti něčeho, co vás zajímá. Nalezení silné sady funkcí vyžaduje experimenty a znalosti v doméně. Některé příznaky jsou pro predikci cíle vhodnější než jiné. Některé funkce také mohou mít silnou korelaci s jinými funkcemi a nebudou do modelu přidávat nové informace. Tyto funkce je možné odebrat.

Chcete-li vytvořit model, můžete použít všechny dostupné funkce nebo vybrat podmnožinu funkcí.

## <a name="choose-and-apply-a-learning-algorithm"></a>Volba a použití algoritmu učení

Vytvořte model pomocí modulu **logistické regrese dvou tříd** a prohlaste ho v datové sadě školení. 

Výsledkem modulu vlakového **modelu** je vyškolený model klasifikace, který se dá použít ke stanovení skóre nových vzorků, aby bylo možné předpovědiovat. Pomocí sady testů můžete vygenerovat skóre z vycvičených modelů. Pak pomocí modulu **vyhodnocení modelu** Analyzujte a porovnejte kvalitu modelů.

Po spuštění experimentu můžete zobrazit výstup z modulu **skóre modelu** kliknutím na výstupní port a výběrem možnosti **vizualizovat**. Výstup obsahuje popisky s skóre a pravděpodobnosti pro popisky.

Nakonec pro otestování kvality výsledků přidejte modul vyhodnotit **model** na plátno experimentu a propojte levý vstupní port s výstupem modulu určení skóre modelu. Spusťte experiment a zobrazte výstup modulu **vyhodnocení modelu** tak, že kliknete na výstupní port a vyberete **vizualizovat**.

## <a name="evaluate"></a>Vyhodnotit
Model logistické regrese má v sadě testů AUC 0,631.

 ![Vyhodnocení](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Další postup

Prozkoumejte další ukázky, které jsou k dispozici pro vizuální rozhraní:

- [Ukázka 1 – regrese: Předpověď ceny automobilu](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 – regrese: Porovnat algoritmy pro předpověď cen automobilu](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpověď úvěrového rizika](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpověď úvěrového rizika (citlivé na náklady)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď změn](ui-sample-classification-predict-churn.md)
