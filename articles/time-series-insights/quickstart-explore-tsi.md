---
title: 'Rychlý Start: Prozkoumejte ukázkové prostředí Gen2 – Azure Time Series Insights Gen2 | Microsoft Docs'
description: Prozkoumejte klíčové funkce prostředí Azure Time Series Insights Gen2 demo.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 03/01/2021
ms.openlocfilehash: 25340af243217313ce6d3f7205c102d4bcd350b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698106"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Rychlý start: Průzkum ukázkového prostředí Azure Time Series Insights Gen2

Tento rychlý Start vám pomůže začít s prostředím Azure Time Series Insights Gen2. V bezplatné ukázce provedete klíčové funkce, které byly přidány do Azure Time Series Insights Gen2.

Ukázkové prostředí Azure Time Series Insights Gen2 obsahuje scénář společnosti Contoso, který pracuje se dvěma farmami větrné turbíny. Každá farma má 10 turbín. Každé turbína má 20 senzorů, které oznamují data každou minutu do Azure IoT Hub. Senzory shromažďují informace o povětrnostních podmínkách, sklonu okna a Yaw pozici. Zaznamenávají se taky informace o výkonu generátoru, chování převodovky a bezpečnostních monitorech.

V tomto rychlém startu se dozvíte, jak pomocí Azure Time Series Insights Gen2 najít užitečné poznatky v datech společnosti Contoso. K lepšímu odhadu kritických chyb a k provedení údržby se taky provádí krátká hlavní příčina.

> [!IMPORTANT]
> Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)   , pokud ho nemáte.

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Prozkoumejte Azure Time Series Insights Gen2 Explorer v ukázkovém prostředí

Azure Time Series Insights Průzkumník Gen2 ukazuje historická data a analýzu hlavní příčiny. Jak začít:

1. Přejít do [ukázkového prostředí farmy společnosti Contoso Wind](https://insights.timeseries.azure.com/preview/samples)  

1. Pokud se zobrazí výzva, přihlaste se k Azure Time Series Insights Průzkumníku Gen2 pomocí vašich přihlašovacích údajů k účtu Azure.

## <a name="work-with-historical-data"></a>Práce s historickými daty

1. Vyberte **Contoso WindFarm Hierarchy**.

   [![WindFarm hierarchie contoso](media/quickstart-explore/quick-start-contoso-1.png)](media/quickstart-explore/quick-start-contoso-1.png#lightbox)

1. Ve **společnosti Contoso 1** se podívejte na vítr turbín **W7**.

   [![W7 ve společnosti Contoso – rostlina 1](media/quickstart-explore/quick-start-contoso-2.png)](media/quickstart-explore/quick-start-contoso-2.png#lightbox)

   1. Změňte rozsah zobrazení na **1/1/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.

      [![Zobrazení rozsahu](media/quickstart-explore/range-setting-1.png)](media/quickstart-explore/range-setting-1.png#lightbox)

      [![Nastavení zobrazení rozsahu](media/quickstart-explore/range-setting-2.png)](media/quickstart-explore/range-setting-2.png#lightbox)

   1. Upravte zobrazení rozsahu pomocí přiblížení **(+)** a ven **(-)** a přesunutím panelu snímků.

      [![Upravit zobrazení rozsahu](media/quickstart-explore/view-range-setting.png)](media/quickstart-explore/view-range-setting.png#lightbox)

   1. Pokud chcete vybrat senzor, vyberte **Contoso závod 1**  >  **W7**  >  **generátor System**  >  **GeneratorSpeed**. Pak zkontrolujte zobrazené hodnoty.

      [![Rychlost generátoru](media/quickstart-explore/quick-start-generator-speed-1.png)](media/quickstart-explore/quick-start-generator-speed-1.png#lightbox)

1. V poslední době společnost Contoso našla požár v větrné **turbíně**. Názory se liší v tom, co způsobilo požár. V Azure Time Series Insights Gen2 se zobrazí senzor výstrah, který byl aktivován během požáru.

   1. Změňte rozsah zobrazení na **3/9/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.
   1. Vyberte **zabezpečení systému**  >  **FireAlert**.

      [![Společnost Contoso našla požár v větrné turbíně](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Zkontrolujte další události v době trvání požáru a zjistěte, co se stalo. Tlak v oleji a aktivní upozornění jsou ve špičkách těsně před požárem.

   1. Vyberte **Rozteč systému**  >  **HydraulicOilPressure**.
   1. Vyberte **Rozteč systému**  >  **ActiveWarning**.

      [![Kontrola dalších událostí okolo stejné doby](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Tlak v oleji a senzory aktivního upozornění se dopravují přímo před požárem. Rozbalení zobrazených časových řad pro kontrolu dalších příznaků, které byly zjevně zavedené do požáru. Oba senzory se v průběhu času pohybovaly konzistentně. Kolísání označují trvalý a worrisome vzorek.

    * Změňte rozsah zobrazení na **2/24/17 20:00:00.00 na 3/10/17 20:00:00.00 (UTC)**.

      [![V oleji se také přeroste tlak a aktivní senzory upozornění.](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Zkoumání dvou let historických dat odhalí další požární událost, která má stejné výkyvy snímačů.

    * Změňte rozsah zobrazení na **1/1/16 na 12/31/17** (všechna data).

      [![Hledání historických vzorů](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Když použijete Azure Time Series Insights Gen2 a telemetrie senzorů, zjistili jsme v historických datech dlouhodobou trendovou skrytý. Díky těmto novým přehledům můžeme:

* Vysvětlete, co se skutečně stalo.
* Opravte problém.
* Umístěte lepší systémy oznámení o výstrahách.

## <a name="root-cause-analysis"></a>Analýza původní příčiny

1. Některé scénáře vyžadují propracované analýzy, které odhalí pokrytí dat. Vyberte Windmill **W6** na datum **6/25**.

    1. Změňte rozsah zobrazení na **6/1/17 20:00:00.00 na 7/1/17 20:00:00.00 (UTC)**.
    1. Vyberte **Contoso rostlinolékařský 1**  >  **W6**  >  **Safety System**  >  **VoltageActuatorSwitchWarning**.

       [![Změňte rozsah zobrazení a vyberte W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Upozornění indikuje problém s napětím ze generátoru. Celkový výstup tohoto generátoru je v rámci běžných parametrů v aktuálním intervalu. Zvýšením našeho intervalu se objeví další vzor. Odkládací adresář je zřejmý.

    1. Odeberte senzor **VoltageActuatorSwitchWarning** .
    1. Vyberte **generátor System**  >  **ActivePower**.
    1. Změňte interval na **3D**.

       [![Změna intervalu na 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Rozšířením časového rozsahu můžeme určit, jestli se problém zastavil, nebo jestli bude pokračovat.

    * Prodlužte časový rozsah až 60 dní.

      [![Prodloužení časového intervalu na 60 dní](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. K zajištění většího kontextu je možné přidat další datové body snímačů. Další senzory, které jsme si vyrozuměli, je úplným porozuměním problému. Pojďme vyřadit značku, aby se zobrazily skutečné hodnoty.

    1. Vyberte **systém generátoru** a potom vyberte tři senzory: **GridVoltagePhase1**, **GridVoltagePhase2** a **GridVoltagePhase3**.
    1. Přetáhněte značku k poslednímu datovému bodu v viditelné oblasti.

       [![Odstranit značku](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dva senzory napětí pracují srovnatelně a v normálních parametrech. Vypadá to, že senzor **GridVoltagePhase3** je příčinou.

1. Když se přidají vysoce kontextová data, vypínání fáze 3 se u tohoto problému zobrazuje ještě více. Nyní máme dobrý zájem na příčinu upozornění. Jsme připraveni tuto problematiku na náš tým údržby odkázat.  

    * Změnou zobrazení překryjete všechny senzory **systému generátorů** ve stejné škále grafu.

      [![Změnit zobrazení tak, aby zahrnovalo vše](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="next-steps"></a>Další kroky

Jste připraveni vytvořit vlastní prostředí Azure Time Series Insights Gen2. Začněte takto:

> [!div class="nextstepaction"]
> [Plánování prostředí Azure Time Series Insights Gen2](./how-to-plan-your-environment.md)

Naučte se používat ukázku a její funkce:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Průzkumník Gen2](./concepts-ux-panels.md)
