---
title: Prozkoumejte ukázky prostředí Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Vysvětlení ukázkovém prostředí Azure Time Series Insights (preview)
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 872969b0e72d32913e4528c5c6446827ae42691b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853052"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Prozkoumejte ukázky prostředí Azure Time Series Insights (preview)

V tomto rychlém startu se dozvíte, jak začít s Azure Time Series Insights (preview) Explorer v bezplatném ukázkovém prostředí. Zjistíte, jak pomocí webového prohlížeče vizualizovat velké objemy historických dat. průmyslového IoT a prohlédnete si klíčové funkce služby Time Series Insights (preview) Průzkumníka.

Azure Time Series Insights poskytuje začátku do konce Platform-As-A-Service nabízí k ingestování a zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas pro ad-hoc zkoumání dat, jakož i provozní analýzy. Time Series Insights je rozlišené míru požadavkům průmyslového IoT nasazení.

Ukázkovém prostředí ukazuje elektrické energie generování společnosti Contoso, užitečné přehledy v jejich datech a provést analýzu krátký původních příčin pomocí služby Time Series Insights. Contoso funguje dvě větrné turbíny farem, každý s deset turbíny a každý turbíny mají 20 snímače vykazujících data každou minutu pro službu Azure IoT Hub. Senzorů shromáždit informace o počasí, výšku okna & úhlu natočení, pozici, generátor výkonu, řazením chování a bezpečný přístup z více monitorů.

Aktualizace služby Time Series Insights slouží k analýze neustále rostoucí sadu dat z poslední dva roky – aktuálně na 40GB – Chcete-li lépe pochopit a předvídání kritické chyby a problémy s pomalým pohybem údržby.

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Průzkumník služby Time Series Insights v ukázkovém prostředí

1. V prohlížeči přejděte na [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Pokud se zobrazí výzva, přihlaste se k Průzkumník Time Series Insights pomocí přihlašovacích údajů svého účtu Azure.

### <a name="demo-step-one"></a>Ukázka kroku, 1

1. Pojďme se podívat na **větrné turbíny #7 ve farmě #1**. Má také provádění.  

    * Akce: Aktualizace zobrazení rozsahu na `1/1/17 20:00 – 3/10/17 20:00 (UTC)` a přidejte `Farm 1 > W7 > Generator > GeneratorSpeed` senzoru.

       ![Rychlý start, jeden][1]

1. Nedávno **Contoso součástí turbíny #7 je**. Teď přejdete k podrobnostem tady. Můžeme vidět upozornění senzor fire aktivovaná během období ohně.

    * Akce: Aktualizace zobrazení rozsahu na `3/9/17 20:00 – 3/10/17 20:00 (UTC)` a přidejte `Safety > FireAlert` senzoru.

      ![Rychlý start dvě][2]

1. Podívejme se, co se stalo v době výskytu ohně. Obě ropy, tlaku a aktivní upozornění vnímal špičku těsně před požár, ale pomocí tohoto bodu, že byla příliš pozdě. aby se předešlo problém.

    * Akce: Přidání `Pitch > HydraulicOilPressure` ze senzorů a `Pitch > ActiveWarning` senzoru.

      ![Tři rychlém startu][3]

1. Oddálit jsme můžeme vidět, byly příznaky vedoucí k ohně. Obě senzorů pohyboval. Tak se to stalo před?

    * Akce: Aktualizace zobrazení rozsahu na `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Rychlý start čtyři][4]

1. Pokud nám prozkoumat celé dva roky dat, můžeme vidět předchozí událost fire stejné znaky. S těmito daty jsme integrovali systémy tak, aby včasné rozpoznávání potíží následujícím způsobem.

    * Akce: Aktualizace zobrazení rozsahu na `1/1/16 – 12/31/17` (všechna data).

       ![Rychlý start pět][5]

### <a name="demo-step-two"></a>Ukázka kroku 2

1. Další problémy se nejnenápadnější a obtížnější k diagnostice. Time Series Insights poskytuje celou řadu funkcí pomáhají sledovat složitých problémů. Tady vidíme výpadek senzor upozornění na `turbine #6` na `6/25`. Ale co se skutečně děje?

    * Akce: Odstraňte aktuální senzory. Potom aktualizujte zobrazení rozsahu `6/1/17 20:00 – 7/1/17 20:00 (UTC)` a přidejte `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Rychlý start šest][6]

1. Toto upozornění označuje potíže s napětí se výstup generátoru. Ale co je příčinou? Celkový výkon výstupu se zobrazila generátor vypadá dobře detailní intervalech. Ale na základě agregace dat, můžeme vidět konečné pokles.

    * Akce: Vzdálené `VoltageActuatorSwitchWarning` a přidejte `Generator > ActivePower` a interval aktualizace `3d`.

       ![Rychlý start sedm][7]

1. Pokud jsme přecházení vpřed v datové sadě, vidíme, že to není pouze přechodný problém. Tento problém pokračuje.

    * Akce: Stisknutím klávesy se šipkou doprava přejděte zobrazení.

       ![Rychlý start osm][8]

1. Můžeme přejít k další. Můžeme dalších datových bodů snímač napětí zobrazit podle fáze. Ale všechny vypadají srovnatelné. Pojďme vyřadit značky zobrazíte skutečnými hodnotami. Zdá se, třetí je nějaký problém s výstupem fáze 3.

    * Akce: `Add Generator > GridVoltagePhase1, 2, & 3`. Odstranit značku na poslední element datapoint viditelná oblast.

       ![Rychlý start osm][8]

1. Pokud jsme zobrazit všechny tři ve stejném měřítku, provede fáze 3 odkládací i zřetelnější. V tuto chvíli jsme připraveni odkazovat tento problém na náš tým údržby s dobrým potenciálních zákazníků na příčina upozornění.  

    * Akce: Aktualizace zobrazení k překrytí všechny senzory na stejné měřítko grafu.

       ![Rychlý start devět][9]

## <a name="next-steps"></a>Další postup

Jste připraveni vytvořit vlastní prostředí Azure Time Series Insights (preview):

> [!div class="nextstepaction"]
> [Plánování prostředí Azure Time Series Insights (preview)](time-series-insights-update-plan.md)

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