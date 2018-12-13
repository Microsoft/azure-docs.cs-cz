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
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888770"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Prozkoumejte ukázky prostředí Azure Time Series Insights (Preview)

V tomto rychlém startu se dozvíte, jak začít s Průzkumníkem služby Azure Time Series Insight (TSI) ve verzi Preview v bezplatném ukázkovém prostředí. Zjistíte, jak pomocí webového prohlížeče vizualizovat velké objemy historických dat. průmyslového IoT a prohlédnete si klíčové funkce Průzkumníka služby Azure Time Series Insights (Preview).

Azure TSI obsahuje začátku do konce Platform-As-A-Service nabídky k ingestování a zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas pro ad-hoc zkoumání dat, jakož i provozní analýzy. Time Series Insights je rozlišené míru požadavkům průmyslového IoT nasazení.

Ukázkovém prostředí ukazuje elektrické energie generování společnost Contoso, pomocí služby TSI na užitečné přehledy v jejich datech a provést analýzu krátký hlavní příčinu. Contoso funguje dvě větrné turbíny farmy, každý s 10 turbíny a každý turbíny mají 20 snímače vykazujících data každou minutu pro službu Azure IoT Hub. Senzorů shromáždit informace o počasí, výšku okna & úhlu natočení, pozici, generátor výkonu, řazením chování a bezpečný přístup z více monitorů.

TSI (Preview) se používá k analýze neustále rostoucí sadu dat z poslední dva roky – aktuálně na 40 GB – Chcete-li lépe pochopit a předvídání kritické chyby a problémy s pomalým pohybem údržby.

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Průzkumník služby Time Series Insights v ukázkovém prostředí

1. V prohlížeči přejděte na [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Pokud se zobrazí výzva, přihlaste se do Průzkumníku TSI pomocí přihlašovacích údajů svého účtu Azure.

### <a name="demo-step-one"></a>Ukázka kroku, 1

1. Pojďme se podívat na **větrné turbíny #7 větrné farmy #1**.  

    * **Akce**: aktualizace zobrazení rozsahu na `1/1/17 20:00 – 3/10/17 20:00 (UTC)` a přidejte `Farm 1 > W7 > Generator > GeneratorSpeed` senzoru. Zobrazí výsledné hodnoty.

       ![Rychlý start, jeden][1]

1. Nedávno **Contoso součástí větrné turbíny #7 je**. Teď přejdete k podrobnostem tady. Můžeme vidět upozornění senzor fire aktivovaná během období ohně.

    * **Akce**: aktualizace zobrazení rozsahu na `3/9/17 20:00 – 3/10/17 20:00 (UTC)` a přidejte `Safety > FireAlert` senzoru.

      ![Rychlý start dvě][2]

1. Podívejme se, co se stalo v době výskytu ohně. Obě ropy, tlaku a aktivní upozornění vnímal špičku těsně před požár, ale pomocí tohoto bodu, že byla příliš pozdě. aby se předešlo problém.

    * **Akce**: Přidat `Pitch > HydraulicOilPressure` ze senzorů a `Pitch > ActiveWarning` senzoru.

      ![Tři rychlém startu][3]

1. Oddálit jsme můžeme vidět, byly příznaky vedoucí k ohně. Obě senzorů pohyboval. Tak se to stalo před?

    * **Akce**: aktualizace zobrazení rozsahu na `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Rychlý start čtyři][4]

1. Pokud nám prozkoumat celé dva roky dat, můžeme vidět předchozí událost fire stejné znaky. S těmito daty jsme integrovali systémy tak, aby včasné rozpoznávání potíží následujícím způsobem.

    * **Akce**: aktualizace zobrazení rozsahu na `1/1/16 – 12/31/17` (všechna data).

       ![Rychlý start pět][5]

### <a name="demo-step-two"></a>Ukázka kroku 2

1. Další problémy se nejnenápadnější a obtížnější k diagnostice. Time Series Insights poskytuje celou řadu funkcí pomáhají sledovat složitých problémů. Tady vidíme výpadek senzor upozornění na `turbine #6` na `6/25`. Ale co se skutečně děje?

    * **Akce**: odebrat aktuální senzory. Potom aktualizujte zobrazení rozsahu `6/1/17 20:00 – 7/1/17 20:00 (UTC)` a přidejte `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Rychlý start šest][6]

1. Toto upozornění označuje potíže s napětí se výstup generátoru. Ale co je příčinou? Celkový výkon výstupu se zobrazila generátor vypadá dobře detailní intervalech. Ale na základě agregace dat, můžeme vidět konečné pokles.

    * **Akce**: vzdálené `VoltageActuatorSwitchWarning` a přidejte `Generator > ActivePower` a interval aktualizace `3d`.

       ![Rychlý start sedm][7]

1. Pokud jsme přecházení vpřed v datové sadě, vidíme, že to není pouze přechodný problém. Tento problém pokračuje.

    * **Akce**: rozšířit časový rozsah na pravé straně.

       ![Rychlý start osm][8]

1. Můžeme přejít k další. Můžeme dalších datových bodů snímač napětí zobrazit podle fáze. Ale všechny vypadají srovnatelné. Pojďme vyřadit značky zobrazíte skutečnými hodnotami. Zdá se, třetí je nějaký problém s výstupem fáze 3.

    * **Akce**: `Add Generator > GridVoltagePhase1, 2, & 3`. Odstranit značku na poslední element datapoint viditelná oblast.

       ![Rychlý start osm][8]

1. Pokud jsme zobrazit všechny tři ve stejném měřítku, provede fáze 3 odkládací i zřetelnější. V tuto chvíli jsme připraveni odkazovat tento problém na náš tým údržby s dobrým potenciálních zákazníků na příčina upozornění.  

    * **Akce**: aktualizace zobrazení k překrytí všechny senzory na stejné měřítko grafu.

       ![Rychlý start devět][9]

## <a name="next-steps"></a>Další postup

Jste připraveni vytvořit vlastní prostředí Azure TSI (Preview):

> [!div class="nextstepaction"]
> [Plánování prostředí Azure TSI (Preview)](time-series-insights-update-plan.md)

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