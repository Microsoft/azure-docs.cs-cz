---
title: 'Úvodní příručka: Prozkoumejte ukázkové prostředí Preview – Přehledy Azure Time Series | Dokumenty společnosti Microsoft'
description: Prozkoumejte klíčové funkce ukázkového prostředí Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 02/07/2020
ms.openlocfilehash: dc4a8da69b0398c6487008c106a9f5bcdb8a885e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77110234"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Úvodní příručka: Prozkoumejte ukázkové prostředí Náhled u Azure Time Series Insights

Tento rychlý start vám zamůže s prostředím Azure Time Series Insights Preview. V bezplatné ukázce si prohlédnete klíčové funkce, které byly přidány do náhledu Time Series Insights.

Demo prostředí Time Series Insights Preview obsahuje společnost, společnost Contoso, která provozuje dvě farmy větrných turbín. Každá farma má 10 turbín. Každá turbína má 20 senzorů, které každou minutu hlásí data do Azure IoT Hub. Senzory shromažďují informace o povětrnostních podmínkách, rozteči lopatek a poloze vycouva. Zaznamenávají se také informace o výkonu generátoru, chování převodovky a bezpečnostních monitorech.

V tomto rychlém startu se dozvíte, jak pomocí time series insights najít užitečné přehledy v datech Contoso. Můžete také provést analýzu krátké hlavní příčiny, abyste lépe předpověděli kritické chyby a provedli údržbu.

> [!IMPORTANT]
> Pokud ho nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Azure.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Prozkoumejte průzkumníka Time Series Insights v ukázkovém prostředí

Průzkumník náhledu přehledů time series ukazuje historická data a analýzu hlavních příčin. Jak začít:

1. Přejděte do ukázkového prostředí [větrné farmy Contoso.](https://insights.timeseries.azure.com/preview/samples)  

1. Pokud se zobrazí výzva, přihlaste se k průzkumníku Time Series Insights pomocí přihlašovacích údajů účtu Azure.

## <a name="work-with-historical-data"></a>Práce s historickými daty

1. V **závodě Contoso 1**se podívejte na větrnou turbínu **W7**.  

   1. Změňte rozsah pohledu na **1/1/17 20:00:00:00 na 3/10/17 20:00:00.00 (UTC).**
   1. Chcete-li vybrat senzor, vyberte **možnost Contoso Plant 1** > **W7** > **Generátor generátoru** > **Generátor Speed**. Potom zkontrolujte hodnoty, které jsou zobrazeny.

      [![W7 v závodě Contoso 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Nedávno, Contoso našel oheň ve větrné turbíně **W7**. Názory se liší o tom, co způsobilo požár. V time series insights se zobrazí senzor požárnívýstrahy, který byl aktivován během požáru.

   1. Změňte rozsah pohledu na **3/9/17 20:00:00:00 na 3/10/17 20:00:00.00 (UTC).**
   1. Vyberte **možnost Požární výstraha bezpečnostního** > **systému**.

      [![Contoso našel oheň ve větrné turbíně W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Zkontrolujte další události v době požáru, abyste pochopili, co se stalo. Tlak oleje a aktivní varování se vyhrotila těsně před požárem.

   1. Vyberte **možnost Pitch System** > **HydraulicoilPressure**.
   1. Vyberte možnost**Aktivní varování** **systému** > pitch .

      [![Kontrola jiných událostí přibližně ve stejnou dobu](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Tlak oleje a aktivní výstražné senzory se vyhrotily těsně před požárem. Rozbalte zobrazené časové řady, abyste si prohlédli další známky, které byly zřejmé, které vedly k požáru. Oba senzory v průběhu času kolísaly konzistentně. Výkyvy naznačují trvalý a znepokojující vzorec.

    * Změňte rozsah pohledu na **2/24/17 20:00:00:00 na 3/10/17 20:00:00.00 (UTC)**.

      [![Tlak oleje a aktivní výstražné senzory také špičaté](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Zkoumání dvouletých historických dat odhalilo další požár, který měl stejné kolísání senzorů.

    * Změňte rozsah pohledu na **1/1/16 na 12/31/17** (všechna data).

      [![Podívejte se na historické vzory](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Pomocí Time Series Insights a telemetrie senzorů jsme objevili dlouhodobý trend skrytý v historických datech. S těmito novými poznatky můžeme:

* Vysvětlete, co se vlastně stalo.
* Opravte problém.
* Zavěste lepší systémy upozornění.

## <a name="root-cause-analysis"></a>Analýza původní příčiny

1. Některé scénáře vyžadují sofistikovanou analýzu k odhalení stop v datech. Vyberte větrný mlýn **W6** v den **6/25**.

    1. Změňte rozsah pohledu na **6/1/17 20:00:00:00 na 7/1/17 20:00:00.00 (UTC)**.
    1. Vyberte **možnost Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

       [![Změna rozsahu pohledu a výběr w6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Varování signalizuje problém s napětím z generátoru. Celkový výkon generátoru je v rámci normálních parametrů v aktuálním intervalu. Zvýšením našeho intervalu se objeví další vzorec. Předání je evidentní.

    1. Vyjměte senzor **VoltageActuatorSwitchWarning.**
    1. Vyberte **možnost ActivePower systému** > **generátoru**.
    1. Změňte interval na **3d**.

       [![Změna intervalu na 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Rozšířením časového rozsahu můžeme určit, zda byl problém zastaven nebo zda pokračuje.

    * Prodlužte časový rozsah na 60 dní.

      [![Prodloužení časového rozpětí na 60 dní](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Další datové body senzoru mohou být přidány, aby poskytovaly větší kontext. Čím více senzorů vidíme, tím plnější je naše chápání problému. Zameškem e-li zobrazte skutečné hodnoty. 

    1. Vyberte **Generátorový systém**a pak vyberte tři senzory: **GridVoltagePhase1**, **GridVoltagePhase2**a **GridVoltagePhase3**.
    1. Přetáhněte značku na poslední datový bod ve viditelné oblasti.

       [![Přetažení značky](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dva napěťové senzory pracují srovnatelně a v rámci normálních parametrů. Vypadá to, že **gridvoltagephase3** senzor je viníkem.

1. S vysoce kontextovými daty přidány, fáze 3 drop-off se zdá být ještě větší problém. Máme dobrou stopu k příčině varování. Jsme připraveni odkázat problém na náš tým údržby.  

    * Změňte zobrazení tak, aby překresloval všechny senzory **generátorového systému** ve stejném měřítku grafu.

      [![Změňte zobrazení tak, aby zahrnovalo vše](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili rychlý start, vyčistěte prostředky, které jste vytvořili:

1. V levé nabídce na [webu Azure Portal](https://portal.azure.com)vyberte Všechny **prostředky**a vyhledejte skupinu prostředků Azure Time Series Insights.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) výběrem **možnosti Odstranit** nebo odebrat jednotlivé prostředky jednotlivě.

## <a name="next-steps"></a>Další kroky

Jste připraveni vytvořit si vlastní prostředí Time Series Insights Preview. Chcete-li začít:

> [!div class="nextstepaction"]
> [Plánování prostředí Náhled přehledů časové řady](time-series-insights-update-plan.md)

Naučte se používat demo a jeho funkce:

> [!div class="nextstepaction"]
> [Průzkumník náhledu přehledů časové řady](time-series-insights-update-explorer.md)