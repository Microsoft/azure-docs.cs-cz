---
title: 'Rychlý Start: Prozkoumejte ukázkové prostředí Preview – Azure Time Series Insights | Microsoft Docs'
description: Prozkoumejte klíčové funkce ukázkového prostředí Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/27/2020
ms.openlocfilehash: c6cfec1d00fbd5b18e01afb0b51598e615cb0946
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189194"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Rychlý Start: Prozkoumejte ukázkové prostředí Azure Time Series Insights Preview

Tento rychlý Start vám pomůže začít s prostředím Azure Time Series Insights Preview. V bezplatné ukázce provedete klíčové funkce, které byly přidány do Time Series Insights Preview.

Ukázkové prostředí Time Series Insights Preview obsahuje firmu společnosti Contoso, která pracuje se dvěma farmami větrné turbíny. Každá farma má 10 turbín. Každé turbína má 20 senzorů, které oznamují data každou minutu do Azure IoT Hub. Senzory shromažďují informace o povětrnostních podmínkách, sklonu okna a Yaw pozici. Zaznamenávají se taky informace o výkonu generátoru, chování převodovky a bezpečnostních monitorech.

V tomto rychlém startu se dozvíte, jak pomocí Time Series Insights najít užitečné poznatky v datech společnosti Contoso. K lepšímu odhadu kritických chyb a k provedení údržby se taky provádí krátká hlavní příčina.

> [!IMPORTANT]
> Vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Azure, pokud ho nemáte.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Prozkoumat Průzkumníka Time Series Insights v ukázkovém prostředí

Průzkumník Preview Time Series Insights ukazuje historická data a analýzu původní příčiny. Jak začít:

1. Přejít do [ukázkového prostředí farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples)  

1. Pokud se zobrazí výzva, přihlaste se do Průzkumníka Time Series Insights pomocí svých přihlašovacích údajů k účtu Azure.

## <a name="work-with-historical-data"></a>Práce s historickými daty

1. Ve **společnosti Contoso 1**se podívejte na vítr turbín **W7**.  

   1. Změňte rozsah zobrazení na **1/1/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.
   1. Pokud chcete vybrat senzor, vyberte **Contoso závod 1** > **W7** > **generátor System** > **GeneratorSpeed**. Pak zkontrolujte zobrazené hodnoty.

      [![W7 ve společnosti Contoso – rostlina 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. V poslední době společnost Contoso našla požár v větrné **turbíně**. Názory se liší v tom, co způsobilo požár. V Time Series Insights se zobrazí senzor výstrah, který byl aktivován během požáru.

   1. Změňte rozsah zobrazení na **3/9/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.
   1. Vyberte **zabezpečení systému** > **FireAlert**.

      [![Společnost Contoso našla požár v větrné turbíně](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Zkontrolujte další události v době trvání požáru a zjistěte, co se stalo. Tlak v oleji a aktivní upozornění jsou ve špičkách těsně před požárem.

   1. Vyberte **Rozteč systému** > **HydraulicOilPressure**.
   1. Vyberte **Rozteč systému** > **ActiveWarning**.

      [![Kontrola dalších událostí okolo stejné doby](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Tlak v oleji a senzory aktivního upozornění se dopravují přímo před požárem. Rozbalení zobrazených časových řad pro kontrolu dalších příznaků, které byly zjevně zavedené do požáru. Oba senzory se v průběhu času pohybovaly konzistentně. Kolísání označují trvalý a worrisome vzorek.

    * Změňte rozsah zobrazení na **2/24/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.

      [![V oleji se také přeroste tlak a aktivní senzory upozornění.](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Zkoumání dvou let historických dat odhalí další požární událost, která má stejné výkyvy snímačů.

    * Změňte rozsah zobrazení na **1/1/16 na 12/31/17** (všechna data).

      [![Hledání historických vzorů](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Pomocí Time Series Insights a telemetrie senzorů jsme zjistili, že v historických datech jsme skryli dlouhodobě trend. Díky těmto novým přehledům můžeme:

* Vysvětlete, co se skutečně stalo.
* Opravte problém.
* Umístěte lepší systémy oznámení o výstrahách.

## <a name="root-cause-analysis"></a>Analýza původní příčiny

1. Některé scénáře vyžadují propracované analýzy, které odhalí pokrytí dat. Vyberte Windmill **W6** na datum **6/25**.

    1. Změňte rozsah zobrazení na **6/1/17 20:00:00.00 na 7/1/17 20:00:00.00 (UTC)**.
    1. Vyberte **Contoso rostlinolékařský 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

       [![Změňte rozsah zobrazení a vyberte W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Upozornění indikuje problém s napětím ze generátoru. Celkový výstup tohoto generátoru je v rámci běžných parametrů v aktuálním intervalu. Zvýšením našeho intervalu se objeví další vzor. Odkládací adresář je zřejmý.

    1. Odeberte senzor **VoltageActuatorSwitchWarning** .
    1. Vyberte **generátor System** > **ActivePower**.
    1. Změňte interval na **3D**.

       [![Změna intervalu na 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Rozšířením časového rozsahu můžeme určit, jestli se problém zastavil, nebo jestli bude pokračovat.

    * Prodlužte časový rozsah až 60 dní.

      [![Prodloužení časového intervalu na 60 dní](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. K zajištění většího kontextu je možné přidat další datové body snímačů. Další senzory, které jsme si vyrozuměli, je úplným porozuměním problému. Pojďme vyřadit značku, aby se zobrazily skutečné hodnoty. 

    1. Vyberte **systém generátoru**a potom vyberte tři senzory: **GridVoltagePhase1**, **GridVoltagePhase2**a **GridVoltagePhase3**.
    1. Přetáhněte značku k poslednímu datovému bodu v viditelné oblasti.

       [![Odstranit značku](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dva senzory napětí pracují srovnatelně a v normálních parametrech. Vypadá to, že senzor **GridVoltagePhase3** je příčinou.

1. Když se přidají vysoce kontextová data, vypínání fáze 3 se u tohoto problému zobrazuje ještě více. Nyní máme dobrý zájem na příčinu upozornění. Jsme připraveni tuto problematiku na náš tým údržby odkázat.  

    * Změnou zobrazení překryjete všechny senzory **systému generátorů** ve stejné škále grafu.

      [![Změnit zobrazení tak, aby zahrnovalo vše](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili rychlý Start, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**a najděte svoji Azure Time Series Insights skupinu prostředků.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

Jste připraveni vytvořit vlastní prostředí Time Series Insights Preview. Začněte takto:

> [!div class="nextstepaction"]
> [Plánování prostředí pro Time Series Insights Preview](time-series-insights-update-plan.md)

Naučte se používat ukázku a její funkce:

> [!div class="nextstepaction"]
> [Průzkumník Preview Time Series Insights](time-series-insights-update-explorer.md)