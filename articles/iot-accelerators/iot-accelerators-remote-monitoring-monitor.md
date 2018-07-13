---
title: Monitorování zařízení IoT z řešení Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak monitorovat zařízení IoT pomocí akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097457"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Kurz: Monitorování zařízení IoT

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování k monitorování připojených zařízení IoT. Pomocí řídicího panelu řešení zobrazíte telemetrii, informace o zařízení, upozornění a klíčové ukazatele výkonu.

V tomto kurzu se k představení těchto funkcí monitorování používají dvě simulovaná zařízení nákladního vozu. Tyto nákladní vozy spravuje organizace Contoso a jsou připojené k akcelerátoru řešení vzdáleného monitorování. Jako operátor společnosti Contoso potřebujete monitorovat polohu a chování vašich nákladních vozů v terénu.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Filtrování zařízení na řídicím panelu
> * Zobrazení telemetrie v reálném čase
> * Zobrazení podrobností o zařízení
> * Zobrazení upozornění ze zařízení
> * Zobrazení klíčových ukazatelů výkonu systému

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci akcelerátoru řešení vzdáleného monitorování.

Pokud jste akcelerátor řešení vzdáleného monitorování ještě nenasadili, měli byste dokončit rychlý start [Nasazení cloudového řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Výběr zařízení k zobrazení

K výběru připojených zařízení, která se zobrazí na stránce **Řídicí panel**, použijte filtry. Pokud chcete zobrazit pouze zařízení **Nákladní vůz**, v rozevíracím seznamu filtru zvolte předdefinovaný filtr **Nákladní vozy**:

[![Vyfiltrování nákladních vozů na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Když použijete filtr, na mapě na stránce **Řídicí panel** se zobrazí pouze zařízení, která splňují podmínky filtru:

[![Na stránce se zobrazí pouze nákladní vozy](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Filtr také určuje, která zařízení se zobrazí v grafu **Telemetrie**:

[![Zobrazení telemetrie nákladních vozů na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

Pokud chcete vytvořit, upravit nebo odstranit filtry, zvolte **Spravovat skupiny zařízení**.

## <a name="view-real-time-telemetry"></a>Zobrazení telemetrie v reálném čase

Akcelerátor řešení vykresluje telemetrická data v reálném čase do grafu na stránce **Řídicí panel**. V horní části grafu telemetrie se zobrazují dostupné typy telemetrie pro zařízení vybraná na základě aktuálního filtru:

[![Typy telemetrie nákladních vozů](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Pokud chcete zobrazit telemetrická data o teplotě, klikněte na **Teplota**:

[![Graf telemetrických dat o teplotě nákladních vozů](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>Použití mapy

Na mapě se zobrazují informace o simulovaných nákladních vozech vybraných na základě aktuálního filtru. Mapu můžete přiblížit a posunout a zobrazit tak více či méně podrobností o umístěních. Barva ikony zařízení na mapě značí, jestli jsou pro dané zařízení aktivní nějaké **výstrahy** nebo **upozornění**. Nalevo od mapy se zobrazuje souhrn počtu **výstrah** a **upozornění**.

Pokud chcete zobrazit podrobnosti o zařízení, posuňte a přibližte mapu tak, abyste našli zařízení, a pak vyberte zařízení na mapě. Pak kliknutím na popisek zařízení otevřete panel **Podrobnosti o zařízení**. Mezi podrobnosti o zařízení patří:

* Poslední hodnoty telemetrie
* Metody, které zařízení podporuje
* Vlastnosti zařízení

[![Zobrazení podrobností o zařízení na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Zobrazení upozornění

Na panelu **Upozornění** se zobrazují podrobné informace o nejnovějších upozorněních z vašich zařízení:

[![Zobrazení upozornění zařízení na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Pomocí filtru můžete upravit časový rozsah pro nedávná upozornění. Ve výchozím nastavení se na panelu zobrazují upozornění za poslední hodinu:

[![Filtrování upozornění podle času](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>Zobrazení klíčových ukazatelů výkonu systému

Na stránce **Řídicí panel** se na panelu **Analýza** zobrazují klíčové ukazatele výkonu systému vypočítané akcelerátorem řešení:

[![Klíčové ukazatele výkonu na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Na řídicím panelu se zobrazují tři klíčové ukazatele výkonu pro upozornění vybraná na základě aktuálních filtrů zařízení a časového rozsahu:

* Počet aktivních upozornění pro pravidla, která aktivovala nejvíce upozornění.
* Poměr upozornění podle typu zařízení.
* Procento upozornění, která jsou kritická.

Způsob agregace klíčových ukazatelů výkonu určují stejné filtry, které nastavují časový rozsah pro upozornění a řídí, která zařízení se zobrazí. Ve výchozím nastavení se na panelu zobrazují klíčové ukazatele výkonu agregované za poslední hodinu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout k dalšímu kurzu, ponechte akcelerátor řešení vzdáleného monitorování nasazený. Pokud chcete snížit náklady na provoz akcelerátoru řešení, když ho nepoužíváte, na panelu nastavení můžete simulovaná zařízení zastavit:

[![Pozastavení telemetrie](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

Až budete připraveni začít s dalším kurzem, můžete simulovaná zařízení restartovat.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho na stránce [Zřízená řešení](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Odstranění řešení](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak pomocí stránky **Řídicí panel** v akcelerátoru řešení vzdáleného monitorování filtrovat a monitorovat simulované nákladní vozy. Informace o tom, jak pomocí akcelerátoru řešení detekovat problémy s připojenými zařízeními, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Detekce problémů se zařízeními připojenými k řešení monitorování](iot-accelerators-remote-monitoring-automate.md)