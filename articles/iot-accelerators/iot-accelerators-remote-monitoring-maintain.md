---
title: Kurz používání upozornění a opravy problémů se zařízeními v řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí upozornění na použití identifikovat a opravovat problémy se zařízeními připojenými k akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1cd1eb9a0bd4b8457ea82303a747acb2553ab707
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451711"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Kurz: Řešení a oprava problémů se zařízeními

V tomto kurzu použijete akcelerátor řešení vzdáleného monitorování k identifikaci a opravě problémů s připojenými zařízeními IoT. Problémy identifikujete pomocí upozornění na řídicím panelu akcelerátoru řešení a pak tyto problémy opravíte spuštěním vzdálených úloh.

Společnost Contoso testuje nový **prototyp** zařízení v terénu. Jako operátor společnosti Contoso si během testování všimnete, že **prototyp** zařízení na řídicím panelu neočekávaně aktivuje upozornění na teplotu. Teď musíte vyšetřit chování tohoto vadného **prototypu** zařízení a odstranit problém.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Vyšetření upozornění ze zařízení
> * Vyřešení problému se zařízením

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

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

V seznamu zařízení s oznámeními vidíte **prototyp** zařízení zodpovědný za aktivaci upozornění na teplotu:

[![Výpis zařízení způsobujících upozornění](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Vyřešení problému

Pokud chcete vyřešit problém s **prototypem** zařízení, musíte na zařízení zavolat metodu **DecreaseTemperature**.

Pokud chcete pracovat na zařízení, vyberte ho v seznamu zařízení s oznámeními a pak zvolte **Úlohy**. **Prototyp** modelu zařízení podporuje šest metod:

[![Zobrazení metod, které zařízení podporuje](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Zvolte **DecreaseTemperature** a nastavte název úlohy na **DecreaseTemperature**. Pak klikněte na **Použít**:

[![Vytvoření úlohy pro snížení teploty](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Pokud chcete sledovat stav úlohy, klikněte na **Zobrazit stav úlohy**. Pomocí zobrazení **Úlohy** můžete sledovat všechny úlohy a volání metod v řešení:

[![Monitorování úlohy pro snížení teploty](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Snížení teploty zařízení můžete zkontrolovat tak, že zobrazíte telemetrii na stránce **Řídicí panel**:

[![Zobrazení snížení teploty](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak pomocí upozornění identifikovat problémy se zařízeními a jak na těchto zařízeních pracovat za účelem vyřešení problémů. Zjistěte, jak na akcelerátor řešení připojit skutečné zařízení, pokračujte články s postupy.

Právě jste se naučili spravovat problémy se zařízeními. Jako další krok navrhujeme zjistit, jak [připojit zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).
