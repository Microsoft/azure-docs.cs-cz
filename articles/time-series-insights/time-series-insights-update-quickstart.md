---
title: 'Rychlý start: Prozkoumejte ukázky prostředí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu'
description: Seznamte se s Azure čas Series Insights ve verzi Preview ukázkovém prostředí.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 604603a145ab360af18ce74748707da9f5f93427
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726399"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Rychlý start: Prozkoumejte Azure čas Series Insights ve verzi Preview ukázkovém prostředí

Tento rychlý start vám pomůže začít s Azure čas Series Insights Preview. Prostřednictvím bezplatné ukázky budete prohlédnete si klíčové funkce, které byly přidány ve verzi Preview čas Series Insights.

Ukázka prostředí ve verzi Preview obsahuje scénář společnost Contoso, který se ovládá dvě větrné turbíny farmy, každý s 10 turbíny. Každý turbíny mají 20 snímače, které vykazují data každou minutu pro službu Azure IoT Hub. Snímačům shromažďovat informace o počasí, výšku okna a yaw pozici. Také generátor výkon, řazením chování a bezpečnostní monitoruje.

 Naučíte se používat Time Series Insights na užitečné přehledy v data společnosti Contoso. Budete také provádět analýzu krátký příčiny lépe předpovědět kritické chyby a provedení údržby.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Prozkoumejte službu v Průzkumníku Time Series Insights v ukázkovém prostředí

Průzkumník čas Series Insights ve verzi Preview demonstruje historických dat a hlavní příčiny potíží analýzy. Jak začít:

1. Vytvoření [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Pokud ještě nebyl vytvořen.

1. Přejděte [Contoso větrné farmy ukázka](https://insights.timeseries.azure.com/preview/samples) prostředí.  

1. Pokud se zobrazí výzva, přihlaste se do Průzkumníka služby Time Series Insights pomocí přihlašovacích údajů svého účtu Azure.

## <a name="work-with-historical-data"></a>Práce s využitím historických dat

1. Podívejte se na větrné turbíny **W7** v **1 zařízení Contoso**.  

    * Aktualizace zobrazení rozsahu na **1/1/17 20:00 do 3/10/17 20:00 (UTC)**.
    * Vyberte **Contoso zařízení 1** > **W7** > **generátor systému** > **GeneratorSpeed** senzor. Pak zkontrolujte výsledné hodnoty.

      [![Rychlý start, jeden](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Contoso nedávno, najdete je v větrné turbíny **W7**. Názory liší o co blízkém požár příčina. Při kontrole podrobněji vidíme, že senzor fire výstrahy se aktivovala během ohně.

    * Aktualizace zobrazení rozsahu na **3/9/17 20:00 do 3/10/17 20:00 (UTC)**.
    * Vyberte **bezpečnost systému** > **FireAlert** senzoru.

      [![Rychlý start dvě](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Kontrolovat další události v době výskytu fire pochopit, co došlo k chybě. Ropa tlak a vnímal špičku těsně před požár jedno aktivní upozornění.

    * Vyberte **rozteč systému** > **HydraulicOilPressure** senzoru.
    * Vyberte **rozteč systému** > **ActiveWarning** senzoru.

      [![Tři rychlém startu](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Přetížení ropy a aktivní upozornění senzorů vnímal špičku těsně před ohně. Rozbalte zobrazené časové řady zobrazíte další příznaky k dispozici vedoucí k ohně. Obě senzorů pohyboval konzistentně časem označující trvalé a znepokojující vzor.

    * Aktualizace zobrazení rozsahu na **2/24/17 20:00 do 3/10/17 20:00 (UTC)**.

      [![Rychlý start čtyři](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Zkoumání dva roky z historických odhalí jinou fire událost s stejné výkyvy senzoru.

    * Aktualizace zobrazení rozsahu na **1/1/16 12/31/17** (všechna data).

      [![Rychlý start pět](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Pomocí Azure Time Series Insights a naše telemetrie ze senzorů, jsme přišli problematické tak pro dlouhodobou trend skryté v našich historická data. Díky těmto poznatkům nové vám vysvětlíme:

> [!div class="checklist"]
> * Co se skutečně stalo
> * Odstranění problému
> * Umístění systémy vynikající oznámení výstrah.

## <a name="root-cause-analysis"></a>Analýza hlavní příčiny

1. Některé scénáře vyžadují sofistikované analýzy odhalit další drobným příčiny v datech. Vyberte windmill **W6** datu **25. ledna 6**

    * Aktualizace zobrazení rozsahu na **6/1/17 20:00 do 7/1/17 20:00 (UTC)**
    * Vyberte **1 zařízení Contoso** > **W6** > **bezpečnost systému** > **VoltageActuatorSwitchWarning**  senzoru.

      [![Rychlý start šest](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. Toto upozornění označuje potíže s napětí se výstup generátoru. Celkové výkon generátoru je zpracovávána v rámci normálního parametry našich na základě aktuálního intervalu. Zvýšením naše interval splatit jiného vzor: existuje jednoznačný pro odložení.

    * Odeberte **VoltageActuatorSwitchWarning** senzoru.
    * Vyberte **generátor systému** > **ActivePower** senzoru.
    * Interval aktualizace **3d**.

      [![Rychlý start sedm](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Po rozbalení časový rozsah, můžeme určit, jestli problém zastavila nebo zda bude pokračovat.

    * Prodloužit časový interval 60 dnů.

      [![Rychlý start osm](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Další datové body senzor lze přidat k zajištění vynikajících kontextu. Další senzory, abychom mohli zobrazit, podrobnější je naše Principy problému. Pojďme vyřadit značky zobrazíte skutečnými hodnotami. 

    * Vyberte **generátor systému** > **GridVoltagePhase1**, **GridVoltagePhase2**, a **GridVoltagePhase3** senzorů .
    * Odstranit značku na poslední datový bod viditelná oblast.

      [![Rychlý start devět](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Tři napěťovými senzory pracujete srovnatelně a v rámci normálního parametrů. To vypadá, **GridVoltagePhase3** senzor je nadměrné spotřeby.

1. S vysoce kontextových dat přidali se zobrazí jako problém ještě více odkládací fáze 3. Nyní jsme připraveni najdete problém na náš tým údržby s dobrým potenciálních zákazníků na příčina upozornění.  

    * Aktualizovat displej tak, aby všechny překryv **generátor systému** senzory ve stejném grafu škálování.

       [![Rychlý start deset](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Další postup

Jste připraveni vytvořit vlastní prostředí čas Series Insights ve verzi Preview:

> [!div class="nextstepaction"]
> [Plánování prostředí čas Series Insights ve verzi Preview](time-series-insights-update-plan.md)