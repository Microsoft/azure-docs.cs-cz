---
title: Rozšířené monitorování v řešení vzdáleného monitorování - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak monitorování zařízení s řídicím panelu řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0456594a4a7776175781968779b4540a98070b78
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Proveďte pokročilé monitorování pomocí řešení vzdáleného monitorování

Tento kurz ukazuje možnosti řídicím panelu vzdáleného monitorování. Zavádět tyto možnosti, tento kurz používá scénáři v aplikaci Contoso IoT.

V tomto kurzu použijete dvě Simulovaná zařízení vůz Contoso se dozvíte, jak monitorovat zařízení na řídicím panelu akcelerátoru řešení. Jako operátor Contoso budete muset sledování umístění a chování vašeho dodávky v poli.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Filtrování zařízení v řídicím panelu
> * Zobrazení telemetrie v reálném čase
> * Zobrazení podrobností o zařízeních
> * Zobrazit výstrahy z vašich zařízení
> * Zobrazení systému klíčové ukazatele výkonu.

## <a name="prerequisites"></a>Požadavky

Chcete-li v tomto kurzu, je třeba nasazené instanci řešení vzdáleného monitorování ve vašem předplatném Azure.

Pokud jste nenasadili řešení vzdáleného monitorování ještě by se měla Dokončit [nasazení vzdálené monitorování akcelerátoru řešení](iot-accelerators-remote-monitoring-deploy.md) kurzu.

## <a name="choose-the-devices-to-display"></a>Vyberte zařízení k zobrazení

Vyberte zařízení, která se zobrazí na **řídicí panel** stránky, použijte filtry. K zobrazení pouze **vůz** zařízení, zvolte integrované **dodávky** filtru v rozevíracím seznamu filtru:

![Filtr pro dodávky na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter.png)

Když použijete filtr, jenom zařízení, které splňují zadaná kritéria filtru zobrazuje v mapě na **řídicí panel** stránky:

![Zobrazení dodávky na mapě](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap.png)

Filtr také určuje zařízení, která se zobrazí v **Telemetrie** grafu:

![Telemetrie vůz zobrazí na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry.png)

Pokud chcete vytvořit, upravit a odstranit filtry, zvolte **Správa filtrů**.

## <a name="view-real-time-telemetry"></a>Zobrazení telemetrie v reálném čase

Řešení akcelerátoru ukazuje zeměpisný podrobné v reálném čase telemetrická data v grafu na **řídicí panel** stránky. Telemetrie graf zobrazuje telemetrické informace pro aktuální filtr vybraná zařízení:

![Výkresu nákladní vůz telemetrie](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview.png)

Hodnoty telemetrická data k zobrazení, vyberte typ telemetrie v horní části grafu:

![Výkresu nákladní vůz telemetrie](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Použití mapy

Mapa zobrazuje informace o simulovaných dodávky vybrána aktuální filtr. Můžete přiblížení a posouvání mapě zobrazíte umístění podrobně vyšší nebo nižší. Ikony zařízení na mapě označují žádné **výstrahy** nebo **upozornění** , které jsou aktivní pro zařízení. Souhrn počtu **výstrahy** a **upozornění** zobrazí nalevo od mapy.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Zobrazit výstrahy z vašich zařízení

Mapy označuje zařízení v aktuální filtr s **výstrahy** a **upozornění**. **Výstrahy** panelu zobrazí podrobné informace o nedávných výstrah ze všech zařízení:

![Zobrazit výstrahy systému na řídicím panelu](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms.png)

Můžete použít **řídicí panel** filtru upravit časové rozpětí pro poslední výstrahy. Ve výchozím nastavení zobrazí na panelu oznámení z za poslední hodinu:

![Filtrování výstrah podle času](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Zobrazení systému klíčové ukazatele výkonu.

**Řídicí panel** stránka zobrazuje systému klíčové ukazatele výkonu:

![Řídicího panelu klíčových ukazatelů výkonu](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis.png)

Můžete použít **řídicí panel** filtru upravit časové rozpětí pro agregaci klíčového ukazatele výkonu. Ve výchozím nastavení zobrazí na panelu klíčových ukazatelů výkonu agregovat přes za poslední hodinu.

## <a name="next-steps"></a>Další postup

Tento kurz vám ukázal, jak používat **řídicí panel** stránky k filtrování a monitorovat simulované dodávky zřízené v řešení vzdáleného monitorování:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrování zařízení v řídicím panelu
> * Zobrazení telemetrie v reálném čase
> * Zobrazení podrobností o zařízeních
> * Zobrazit výstrahy z vašich zařízení
> * Zobrazení systému klíčové ukazatele výkonu.

Teď, když jste se naučili monitorování zařízení, navrhované další kroky jsou další postup:

* [Zjistit problémy při použití pravidla na základě prahové hodnoty](iot-accelerators-remote-monitoring-automate.md).
* [Správa a konfigurace zařízení](iot-accelerators-remote-monitoring-manage.md).
* [Řešení potíží a opravám problémů zařízení](iot-accelerators-remote-monitoring-maintain.md).
* [Testování řešení s Simulovaná zařízení](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->