---
title: Kurz monitorování zařízení IoT z řešení Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak monitorovat zařízení IoT pomocí akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d31ea1fe579e5ac7a846c1c0d03012d70be9884d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159344"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Kurz: Monitorování zařízení IoT

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování k monitorování připojených zařízení IoT. Pomocí řídicího panelu řešení zobrazíte telemetrii, informace o zařízení, upozornění a klíčové ukazatele výkonu.

Tento kurz používá dvě simulovaná zařízení nákladního vozu, která odesílají polohu, rychlost a telemetrická data o teplotě nákladu. Tyto nákladní vozy spravuje organizace Contoso a jsou připojené k akcelerátoru řešení vzdáleného monitorování. Jako operátor společnosti Contoso potřebujete monitorovat polohu a chování jednoho z vašich nákladních vozů (truck-02) v terénu.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Filtrování zařízení na řídicím panelu
> * Zobrazení telemetrie v reálném čase
> * Zobrazení podrobností o zařízení
> * Zobrazení upozornění ze zařízení
> * Zobrazení klíčových ukazatelů výkonu systému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [iot-iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Výběr zařízení k zobrazení

K výběru připojených zařízení, která se zobrazí na stránce **Řídicí panel**, použijte filtry. Pokud chcete zobrazit pouze zařízení **Nákladní vůz**, v rozevíracím seznamu filtru zvolte předdefinovaný filtr **Nákladní vozy**:

[![Vyfiltrování nákladních vozů na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Když použijete filtr, na mapě a panelu telemetrie na stránce **Řídicí panel** se zobrazí pouze zařízení, která splňují podmínky filtru. Uvidíte, že k akcelerátoru řešení jsou připojené dva nákladní vozy (včetně nákladního vozu truck-02):

[![Na stránce se zobrazí pouze nákladní vozy](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Pokud chcete vytvořit, upravit nebo odstranit filtry, klikněte na **Spravovat skupiny zařízení**.

## <a name="view-real-time-telemetry"></a>Zobrazení telemetrie v reálném čase

Akcelerátor řešení vykresluje telemetrická data v reálném čase do grafu na stránce **Řídicí panel**. V horní části grafu telemetrie se zobrazují dostupné typy telemetrie pro zařízení (včetně nákladního vozu truck-02) vybraná na základě aktuálního filtru. Ve výchozím nastavení graf zobrazuje zeměpisnou šířku nákladních vozů a truck-02 vypadá, že stojí:

[![Typy telemetrie nákladních vozů](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Pokud chcete zobrazit telemetrická data o teplotě nákladních vozů, klikněte na **Teplota**. Uvidíte, jak se teplota za poslední hodinu u nákladního vozu truck-02 měnila:

[![Graf telemetrických dat o teplotě nákladních vozů](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Zobrazení mapy

Na mapě se zobrazují informace o simulovaných nákladních vozech vybraných na základě aktuálního filtru. Mapu můžete přiblížit a posunout a zobrazit tak více či méně podrobností o umístěních. Barva ikony zařízení na mapě značí, jestli jsou pro dané zařízení aktivní nějaké **výstrahy** (tmavě modrá) nebo **upozornění** (červená). Nalevo od mapy se zobrazuje souhrn počtu **výstrah** a **upozornění**.

Pokud chcete zobrazit podrobnosti o nákladním voze truck-02, posuňte a přibližte mapu tak, abyste ho našli, a pak ho na mapě vyberte. Pak kliknutím na popisek zařízení otevřete panel **Podrobnosti o zařízení**. Mezi podrobnosti o zařízení patří:

* Poslední hodnoty telemetrie
* Metody, které zařízení podporuje
* Vlastnosti zařízení

[![Zobrazení podrobností o zařízení na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Zobrazení upozornění

Na panelu **Upozornění** se zobrazují podrobné informace o nejnovějších upozorněních ze zařízení. Upozornění z nákladního vozu truck-02 poukazují na vyšší než běžnou teplotu nákladu:

[![Zobrazení upozornění zařízení na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Pomocí filtru můžete upravit časový rozsah pro nedávná upozornění. Ve výchozím nastavení se na panelu zobrazují upozornění za poslední hodinu.

## <a name="view-the-system-kpis"></a>Zobrazení klíčových ukazatelů výkonu systému

Na stránce **Řídicí panel** se na panelu **Analýza** zobrazují klíčové ukazatele výkonu systému vypočítané akcelerátorem řešení:

[![Klíčové ukazatele výkonu na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Na řídicím panelu se zobrazují tři klíčové ukazatele výkonu pro upozornění vybraná na základě aktuálních filtrů zařízení a časového rozsahu:

* Počet aktivních upozornění pro pravidla, která aktivovala nejvíce upozornění.
* Poměr upozornění podle typu zařízení.
* Procento upozornění, která jsou kritická.

U nákladního vozu truck-02 jsou všechny výstrahy upozorněním na vyšší než běžnou teplotu nákladu.

Způsob agregace klíčových ukazatelů výkonu určují stejné filtry, které nastavují časový rozsah pro upozornění a řídí, která zařízení se zobrazí. Ve výchozím nastavení se na panelu zobrazují klíčové ukazatele výkonu agregované za poslední hodinu.

[!INCLUDE [iot-iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak pomocí stránky **Řídicí panel** v akcelerátoru řešení vzdáleného monitorování filtrovat a monitorovat simulované nákladní vozy. Informace o tom, jak pomocí akcelerátoru řešení detekovat problémy s připojenými zařízeními, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Detekce problémů se zařízeními připojenými k řešení monitorování](iot-accelerators-remote-monitoring-automate.md)