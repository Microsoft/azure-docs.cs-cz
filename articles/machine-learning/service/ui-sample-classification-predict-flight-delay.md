---
title: 'Klasifikace: Predikce zpoždění letů'
titleSuffix: Azure Machine Learning service
description: V tomto článku se dozvíte, jak vytvořit model k předpovědi zpoždění letů pomocí přetahování myší vizuální rozhraní a vlastní kód R strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607634"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Ukázka 6 – klasifikace: Předpověď zpoždění letů pomocí jazyka R

Tento experiment používá historické cestě a data o počasí předpovědět, pokud naplánované civilní let odloží o bude zpozdit o víc než 15 minut.

Tento problém můžete použijí jako problém klasifikace, předpověď dvě třídy – zpoždění, nebo na čas. Chcete-li sestavení klasifikátoru, tento model pomocí velkého počtu příklady z historických zapisovači letových údajů.

Tady je graf konečný experiment:

[![Graf experimentu](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Vyberte **otevřít** tlačítko pro 6 ukázkový experiment:

    ![Otevřete experiment](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Získání dat

Tento experiment používá **zpoždění letů** datové sady. To je součástí TranStats shromažďování dat z USA Ministerstva dopravy. Datová sada obsahuje informace zpoždění letu od října 2013. Před nahráním dat pro vizuální rozhraní, jejich předběžné zpracování následujícím způsobem:

* Vyfiltrován, aby obsahoval 70 za nejvytíženější letiště v kontinentální části USA.
* Pro zrušit lety, relabeled při zpoždění ve více než 15 minut.
* Odfiltrovat odkloněných lety.
* Vybrané sloupce 14.

K doplnění zapisovači letových údajů **datovou sadu počasí** se používá. Data o počasí obsahuje po hodinách na základě pozemního počasí pozorování z NOAA a představuje připomínek letiště meteorologická stanice, pokrývající stejné časové období. dubna – říjen 2013. Než nahrajete do Azure ML vizuální rozhraní, jejich předběžné zpracování následujícím způsobem:

* ID meteorologická stanice se mapují na odpovídající letiště ID.
* Stanice počasí nejsou spojena s 70 za nejvytíženější letiště byly odebrány.
* Sloupec data byla rozdělit na samostatné sloupce: Rok, měsíc a den.
* Vybrané sloupce 26.

## <a name="pre-process-the-data"></a>Předběžně zpracovat data

Datové sady obvykle vyžaduje některé předběžného zpracování předtím, než mohou být analyzovány.

![proces dat](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Zapisovači letových údajů

Sloupce **dopravce**, **OriginAirportID**, a **DestAirportID** se uloží jako celá čísla. Nicméně jsou zařazené do kategorií atributy, použijte **upravit Metadata** modul pro převod do kategorií.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Potom použijte **výběr sloupců** v modulu datovou sadu chcete vyloučit z datové sady sloupců, které jsou leakers možný cíl: **DepDelay**, **DepDel15**, **ArrDelay**, **zrušena**, **rok**. 

Čas plánované odeslání připojit letu záznamy s hodinovou záznamy o počasí, lze použijte jako jeden z klíčů spojení. Provedete spojení musí CSRDepTime sloupci zaokrouhlená dolů na nejbližší hodiny, které se provádí v **spustit skript jazyka R** modulu. 

### <a name="weather-data"></a>Data o počasí

Sloupce, které obsahují velká část chybějící hodnoty budou vyloučeny pomocí **sloupce projektu** modulu. Tyto sloupce zahrňte všechny sloupce s hodnotou řetězce: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, a **StationPressure**.

**Vyčištění chybějících dat** modulu se následně použije na zbývající sloupce se mají odebrat řádky s chybějící data.

Doby zjišťování počasí se zaokrouhluje na nejbližší celé hodiny. Naplánovaný let časy a časy počasí pozorování jsou zaokrouhleny opačným směrem a ujistěte se, že používá model pouze počasí před časem letu. 

Protože data o počasí v místním čase hlásí, jsou rozdíly v časových pásmech představoval tak, že sloupce časového pásma z naplánované odeslání čas a čas pozorování počasí. Tato operace se provádějí pomocí **spustit skript jazyka R** modulu.

### <a name="joining-datasets"></a>Připojení k datové sady

Flight záznamy, které jsou spojeny s data o počasí v původu letu (**OriginAirportID**) pomocí **připojení dat** modulu.

 ![připojení k cestě a weather podle zdroje](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Letu záznamy jsou spojeny s daty o počasí pomocí cílové letu (**DestAirportID**).

 ![Připojte se k cestě a weather podle cílové](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Příprava trénování a ukázek testu

**Rozdělení dat** modulu rozdělí data do dubna si září záznamy pro trénování a října záznamy pro test.

 ![Rozdělit, školení a údaje o testu](media/ui-sample-classification-predict-flight-delay/split.png)

Rok, měsíc a časové pásmo sloupce se odeberou z trénovací datové sady pomocí modulu výběr sloupců.

## <a name="define-features"></a>Definice funkcí

V machine learning jsou funkce jednotlivé měřitelné vlastnosti něčeho něco, co vás zajímá. Hledání propracované sady funkcí vyžaduje experimentování a domény znalosti. Některé příznaky jsou pro predikci cíle vhodnější než jiné. Také některé funkce možná mají silnou korelaci s jinými funkcemi a nebudou do modelu přidat nové informace. Tyto funkce se dá odebrat.

Sestavení modelu, můžete používat všechny funkce, které jsou k dispozici, nebo vybrat podmnožinu funkcí.

## <a name="choose-and-apply-a-learning-algorithm"></a>Volba a použití algoritmu učení

Vytvořit pomocí modelu **Two-Class logistické regrese** modul a jeho trénování na trénovací datové sady. 

Výsledkem **Train Model** modulu je natrénovaného klasifikačního model, který slouží ke stanovení skóre pro nové vzorky a k vytváření predikcí. Použití testu nastavte k vygenerování skóre z trénované modely. Potom použijte **Evaluate Model** modulu analyzovat a porovnat kvality modelů.

Po spuštění testu se zobrazí výstup **Score Model** modulu tak, že kliknete na výstupní port a vyberete **vizualizovat**. Výstup bude obsahovat skóre popisky a pravděpodobnosti pro popisky.

Nakonec pro otestování kvality výsledků, přidejte **Evaluate Model** modulů na experiment plátno a propojte jej s levým vstupním portem k výstupu modulu určení skóre modelu. Spusťte experiment a zobrazte výstup **Evaluate Model** modulu, že kliknete na výstupní port a vyberete **vizualizovat**.

## <a name="evaluate"></a>Vyhodnotit
Model logistické regrese má AUC 0.631 testovacího nastavení.

 ![Vyhodnocení](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Další postup

Prozkoumejte službu k dispozici pro vizuální rozhraní ukázky:

- [Ukázka 1 - regrese: Předpovídat cenu automobilu představuje jeden](ui-sample-regression-predict-automobile-price-basic.md)
- [Ukázka 2 - regrese: Porovnání algoritmy pro předpověď cen automobilů](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace: Předpovědět úvěrové riziko](ui-sample-classification-predict-credit-risk-basic.md)
- [Ukázka 4 – klasifikace: Předpovědět úvěrové riziko (náklady na citlivé)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: Předpověď výpovědi](ui-sample-classification-predict-churn.md)
