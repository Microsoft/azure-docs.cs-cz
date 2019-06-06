---
title: 'Rychlý start: Prozkoumejte ukázky prostředí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu'
description: Seznamte se s Azure čas Series Insights ve verzi Preview ukázkovém prostředí.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 0bbab2acc9bf9e22e1d3c36336aa9dad04b0b73a
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688502"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Rychlý start: Prozkoumejte Azure čas Series Insights ve verzi Preview ukázkovém prostředí

Tento rychlý start vám pomůže začít s prostředím Azure čas Series Insights ve verzi Preview. V bezplatné ukázky prohlédnete si klíčové funkce, které byly přidány do čas Series Insights ve verzi Preview.

Čas Series Insights ve verzi Preview ukázkovém prostředí obsahuje společnosti scénář, společnosti Contoso, který se ovládá dvě větrné turbíny farmy. Každou farmu má 10 turbíny. Každý turbíny mají 20 snímače, které vykazují data každou minutu pro službu Azure IoT Hub. Snímačům shromažďovat informace o počasí, výšku okna a yaw pozici. Také se zaznamenává informace o výkonu generátor, řazením chování a bezpečný přístup z více monitorů.

V tomto rychlém startu se dozvíte, jak pomocí služby Time Series Insights užitečné přehledy v data společnosti Contoso. Také proveďte analýzu krátký původní příčiny lépe předpovědět kritické chyby a provedení údržby.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Prozkoumejte službu v Průzkumníku Time Series Insights v ukázkovém prostředí

Průzkumník čas Series Insights ve verzi Preview demonstruje historických dat a hlavní příčiny potíží analýzy. Jak začít:

1. Vytvoření [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) pokud ho nemáte.

1. Přejděte [Contoso větrné farmy ukázka](https://insights.timeseries.azure.com/preview/samples) prostředí.  

1. Pokud se zobrazí výzva, přihlaste se do Průzkumníka služby Time Series Insights pomocí přihlašovacích údajů k účtu Azure.

## <a name="work-with-historical-data"></a>Práce s využitím historických dat

1. V **1 zařízení Contoso**, podívejte se na větrné turbíny **W7**.  

   1. Změnit rozsah zobrazení k **1/1/17 20:00 do 3/10/17 20:00 (UTC)** .
   1. Chcete-li vybrat senzor, vyberte **1 zařízení Contoso** > **W7** > **generátor systému** > **GeneratorSpeed** . Zkontrolujte hodnoty, které jsou zobrazeny.

      [![W7 v závodě Contoso 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Contoso nedávno, najdete je v větrné turbíny **W7**. Názory liší o tom, co způsobilo ohně. V Time Series Insights vidíme, že senzor fire výstrahy se aktivovala během ohně.

   1. Změnit rozsah zobrazení k **3/9/17 20:00 do 3/10/17 20:00 (UTC)** .
   1. Vyberte **bezpečnost systému** > **FireAlert**.

      [![Contoso je součástí větrné turbíny W7](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Kontrolovat další události v době výskytu fire pochopit, co došlo k chybě. Ropa tlaku a vnímal špičku těsně před požár jedno aktivní upozornění.

   1. Vyberte **prezentační systém** > **HydraulicOilPressure**.
   1. Vyberte **prezentační systém** > **ActiveWarning**.

      [![Kontrolovat další události přibližně ve stejnou dobu](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Přetížení ropy a aktivní upozornění senzorů vnímal špičku těsně před ohně. Rozbalte zobrazené časové řady zobrazíte další příznaky, které byly zřejmá, vedoucí k ohně. Obě senzorů konzistentně pohyboval v čase. Výkyvy označení trvalé a znepokojující vzor.

    * Změnit rozsah zobrazení k **2/24/17 20:00 do 3/10/17 20:00 (UTC)** .

      [![Ropa tlaku a také vnímal špičku aktivní upozornění senzorů](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Zkoumání dva roky historických dat zobrazí jiné fire události, který měl stejné kolísání senzor.

    * Změnit rozsah zobrazení k **1/1/16 12/31/17** (všechna data).

      [![Vyhledávat historických vzory](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Pomocí služby Time Series Insights a naše telemetrie ze senzorů, jsme přišli problematické tak pro dlouhodobou trend skryté v historická data. Tyto nové poznatky můžeme:

> [!div class="checklist"]
> * Vysvětluje, co skutečně došlo k chybě.
> * Odstranění problému.
> * Umístění systémy vynikající oznámení výstrah.

## <a name="root-cause-analysis"></a>Analýza původní příčiny

1. Některé scénáře vyžadují sofistikované analýzy odhalit další drobným příčiny v datech. Vyberte windmill **W6** datu **6/25**.

    1. Změnit rozsah zobrazení k **6/1/17 20:00 do 7/1/17 20:00 (UTC)** .
    1. Vyberte **Contoso zařízení 1** > **W6** > **bezpečnost systému** > **VoltageActuatorSwitchWarning**.

       [![Změnit rozsah zobrazení a vyberte W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. Toto upozornění označuje potíže s napětí se výstup generátoru. Celkové výkon generátoru je zpracovávána v rámci normálního parametry v aktuální interval. Zvýšením naše interval splatit jiný model. Jednoznačného pro odložení je evidentní.

    1. Odeberte **VoltageActuatorSwitchWarning** senzoru.
    1. Vyberte **generátor systému** > **ActivePower**.
    1. Změna intervalu **3d**.

       [![Změna intervalu 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Po rozbalení časový rozsah, můžeme určit, jestli problém zastavila nebo zda bude pokračovat.

    * Prodloužit časový interval 60 dnů.

      [![Prodloužit časový interval 60 dní](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Další datové body senzor lze přidat k zajištění větší kontextu. Další senzorů jsme zobrazení, tím plnější je naše Principy problému. Pojďme vyřadit značky zobrazíte skutečnými hodnotami. 

    1. Vyberte **generátor systému**a pak vyberte tři senzory: **GridVoltagePhase1**, **GridVoltagePhase2**, a **GridVoltagePhase3**.
    1. Odstranit značku na poslední datový bod viditelná oblast.

       [![Vyřadit značku](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Dvě napěťovými senzory pracujete srovnatelně a v rámci normálního parametrů. To vypadá, **GridVoltagePhase3** senzor je nadměrné spotřeby.

1. S vysoce kontextová data přidaná se zobrazí pro fázi 3 odložení ještě víc informací, které se tento problém. Teď máme dobré potenciálních zákazníků na příčina upozornění. Jsme připraveni k odkazování na náš tým údržby problém.  

    * Změnit zobrazení na všechny překryv **generátor systému** senzory ve stejném grafu škálování.

      [![Změna zobrazení zahrnul všechno](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Další postup

Jste připraveni vytvořit vlastní prostředí čas Series Insights ve verzi Preview. Spuštění:

> [!div class="nextstepaction"]
> [Plánování prostředí čas Series Insights ve verzi Preview](time-series-insights-update-plan.md)

Naučte se orientovat na ukázku a jejich funkce:

> [!div class="nextstepaction"]
> [V Průzkumníku čas Series Insights ve verzi Preview](time-series-insights-update-explorer.md)
