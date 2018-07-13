---
title: Používání upozornění a opravy problémů se zařízeními v řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí upozornění na použití identifikovat a opravovat problémy se zařízeními připojenými k akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/18/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9607705220450b30d2ffaf0f2be9fa2a5664b879
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081784"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Řešení potíží a odstraňování problémů se zařízeními

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování k identifikaci a opravě problémů s připojenými zařízeními IoT. Problémy identifikujete pomocí upozornění na řídicím panelu akcelerátoru řešení a pak tyto problémy opravíte spuštěním vzdálených úloh.

Společnost Contoso testuje nový **prototyp** zařízení v terénu. Jako operátor společnosti Contoso si během testování všimnete, že **prototyp** zařízení na řídicím panelu neočekávaně aktivuje upozornění na teplotu. Teď musíte vyšetřit chování tohoto vadného **prototypu** zařízení a odstranit problém.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Vyšetření upozornění ze zařízení
> * Vyřešení problému se zařízením

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci akcelerátoru řešení vzdáleného monitorování.

Pokud jste akcelerátor řešení vzdáleného monitorování ještě nenasadili, měli byste dokončit rychlý start [Nasazení cloudového řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).

## <a name="investigate-an-alert"></a>Vyšetření upozornění

Na stránce **Řídicí panel** si všimnete neočekávaných upozornění na teplotu pocházejících z pravidla přidruženého k **prototypům** zařízení:

[![Zobrazená upozornění na řídicím panelu](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Pokud chcete problém vyšetřit blíže, vedle upozornění zvolte možnost **Prozkoumat upozornění**:

[![Zkoumání upozornění na řídicím panelu](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

Podrobné zobrazení upozornění obsahuje:

* Čas aktivace upozornění
* Informace o stavu zařízení přidružených k tomuto upozornění
* Telemetrie ze zařízení přidružených k upozornění

[![Podrobnosti o upozornění](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Pokud chcete upozornění potvrdit, vyberte všechny **Výskyty upozornění** a zvolte **Potvrdit**. Touto akcí dáte ostatním operátorům najevo, že jste upozornění viděli a pracujete na něm:

[![Potvrzení upozornění](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Když upozornění potvrdíte, stav výskytu se změní na **Potvrzeno**.

V seznamu vidíte **prototyp** zařízení zodpovědný za aktivaci upozornění na teplotu:

[![Výpis zařízení způsobujících upozornění](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Vyřešení problému

Pokud chcete vyřešit problém s **prototypem** zařízení, musíte na zařízení zavolat metodu **DecreaseTemperature**.

Pokud chcete pracovat na zařízení, vyberte ho v seznamu zařízení a pak zvolte **Úlohy**. Model **prototypu** zařízení určuje šest metod, které zařízení musí podporovat:

[![Zobrazení metod, které zařízení podporuje](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Zvolte **DecreaseTemperature** a nastavte název úlohy na **DecreaseTemperature**. Pak zvolte **Použít**:

[![Vytvoření úlohy pro snížení teploty](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Pokud chcete sledovat stav úlohy, klikněte na **Zobrazit stav úlohy**. Pomocí zobrazení **Úlohy** můžete sledovat všechny úlohy a volání metod v řešení:

[![Monitorování úlohy pro snížení teploty](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Snížení teploty zařízení můžete zkontrolovat tak, že zobrazíte telemetrii na stránce **Řídicí panel**:

[![Zobrazení snížení teploty](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak pomocí upozornění identifikovat problémy se zařízeními a jak na těchto zařízeních pracovat za účelem vyřešení problémů. Informace o tom, jak k akcelerátoru řešení připojit fyzické zařízení, najdete v článcích s postupy.

Právě jste se naučili spravovat problémy se zařízeními. Jako další krok navrhujeme zjistit, jak [připojit zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).
