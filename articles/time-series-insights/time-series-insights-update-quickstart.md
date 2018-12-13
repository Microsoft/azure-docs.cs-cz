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
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276822"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Rychlý start: Prozkoumejte Azure čas Series Insights ve verzi Preview ukázkovém prostředí

V tomto rychlém startu se dozvíte, jak pomocí Průzkumníka Azure čas řadu přehledů ve verzi Preview v bezplatném ukázkovém prostředí. Se dozvíte, jak pomocí webového prohlížeče vizualizovat velké objemy historických dat průmyslového IoT a můžete si klíčové funkce Průzkumníka čas Series Insights ve verzi Preview.

Time Series Insights poskytuje začátku do konce platforma jako služba (PaaS). Může ingestovat, zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas pro zkoumání dat improvizovanými. Poskytuje také provozní analýzy. Time Series Insights je rozlišené nabídky, který je vytvořený na míru pro jedinečné potřeby průmyslové nasazení IoT.

Ukázkovém prostředí ukazuje elektrické energie generování společnosti Contoso. V prostředí pomocí služby Time Series Insights užitečné přehledy v data společnosti Contoso a provést analýzu krátký hlavní příčinu. Contoso používá dva větrné turbíny farmy, každý s 10 turbíny. Každý turbíny mají 20 snímače, které vykazují data každou minutu pro službu Azure IoT Hub. Snímačům shromažďovat informace o počasí, výšku okna a yaw pozici, generátor výkonu, řazením chování a bezpečný přístup z více monitorů.

Čas Series Insights ve verzi Preview můžete použít k analýze společnosti Contoso neustále se rozšiřujícímu datové sady z poslední dva roky, který je momentálně obsazená na 40 GB. To vám mohou pomoci při lepší pochopení a předvídání kritické chyby a problémy s pomalým pohybem údržby.

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Prozkoumejte službu v Průzkumníku Time Series Insights v ukázkovém prostředí

1. V prohlížeči přejděte [prostředí Contoso větrné farmy](https://insights.timeseries.azure.com/preview/samples).  

1. Pokud se zobrazí výzva, přihlaste se do Průzkumníka služby Time Series Insights pomocí přihlašovacích údajů účtu Azure.

### <a name="demo-step-1"></a>Ukázka krok 1

1. Pojďme se podívat na větrné turbíny **W7** v **1 zařízení Contoso**.  

    * **Akce**: Aktualizovat rozsah zobrazení k **1/1/17 20:00 do 3/10/17 20:00 (UTC)**, přidejte **1 zařízení Contoso** > **W7** > **generátor systému**   >  **GeneratorSpeed** snímače a poté zobrazte výsledné hodnoty.

       ![Rychlý start, jeden][1]

1. Contoso nedávno, najdete je v větrné turbíny **W7**. Teď přejdete k podrobnostem tady. Můžeme vidět, že senzor fire výstrahy se aktivovala během ohně.

    * **Akce**: Aktualizace zobrazení rozsahu na **3/9/17 20:00 do 3/10/17 20:00 (UTC)** a přidejte **bezpečnost systému** > **FireAlert** senzoru.

      ![Rychlý start dvě][2]

1. Podívejme se, co se stalo v době výskytu ohně. Ropa tlak a aktivní upozornění vnímal špičku těsně před požár, ale během této doby je příliš pozdě, abyste zabránili tomuto problému.

    * **Akce**: Přidat **rozteč systému** > **HydraulicOilPressure** ze senzorů a **rozteč systému** > **ActiveWarning**senzoru.

      ![Tři rychlém startu][3]

1. Oddálit jsme můžeme vidět, byly příznaky vedoucí k ohně. Obě senzorů pohyboval. Proto se tento problém stalo před?

    * **Akce**: Aktualizace zobrazení rozsahu na **2/24/17 20:00 do 3/10/17 20:00 (UTC)**.

      ![Rychlý start čtyři][4]

1. Pokud nám prozkoumat celé dva roky dat, můžeme vidět předchozí událost fire stejné znaky. S těmito daty můžeme sestavit systémy tak, aby odhalte problémy s tímto způsobem jeden již v rané fázi.

    * **Akce**: Aktualizace zobrazení rozsahu na **1/1/16 12/31/17** (všechna data).

       ![Rychlý start pět][5]

### <a name="demo-step-2"></a>Ukázka krok 2

1. Další problémy se nejnenápadnější a obtížnější k diagnostice. Time Series Insights poskytuje celou řadu způsobů, jak pomáhají sledovat složitých problémů. Tady vidíme výpadek senzor upozornění na **W6** na **6/25**. Ale co se skutečně děje?

    * **Akce**: Odebrat aktuální senzory, aktualizujte zobrazení rozsahu **6/1/17 20:00 do 7/1/17 20:00 (UTC)** a pak přidejte **1 zařízení Contoso** > **W6**  >  **Bezpečnost systému** > **VoltageActuatorSwitchWarning** senzoru.

       ![Rychlý start šest][6]

1. Toto upozornění označuje potíže s napětí se výstup generátoru. Ale co je příčinou? Celkový výkon výstupu se zobrazila generátor vypadá dobře detailní intervalech. Ale na základě agregace dat, můžeme vidět konečné pro odložení.

    * **Akce**: Odeberte **VoltageActuatorSwitchWarning** senzor, přidejte **generátor systému** > **ActivePower** ze senzorů a aktualizace interval **3d**.

       ![Rychlý start sedm][7]

1. Pokud jsme přecházení vpřed v datové sadě, vidíme, že tento problém není přechodná. Bude pokračovat.

    * **Akce**: Rozšiřte časový rozsah na pravé straně.

       ![Rychlý start osm][8]

1. Můžeme přejít k další. Můžeme přidat dalších datových bodů snímač napětí zobrazit podle fáze. Prohlédněte si všechny datové body srovnatelné ale. Pojďme vyřadit značky zobrazíte skutečnými hodnotami. Pravděpodobně je nějaký problém s výstupem fáze 3.

    * **Akce**: Přidat **generátor systému** > **GridVoltagePhase1**, **GridVoltagePhase2**, a **GridVoltagePhase3** senzory. Odstranit značku na poslední datový bod viditelná oblast.

       ![Rychlý start osm][8]

1. Můžeme zobrazit všechny tři datových bodů ve stejném měřítku, odkládací fáze 3 se zobrazí i zřetelnější. V tuto chvíli jsme připraveni k odkazování na náš tým údržby s dobrým potenciálních zákazníků na příčina upozornění problém.  

    * **Akce**: Aktualizujte zobrazení k překrytí všechny senzory na stejné měřítko grafu.

       ![Rychlý start devět][9]

## <a name="next-steps"></a>Další postup

Jste připraveni vytvořit vlastní prostředí čas Series Insights ve verzi Preview:

> [!div class="nextstepaction"]
> [Plánování prostředí čas Series Insights ve verzi Preview](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
