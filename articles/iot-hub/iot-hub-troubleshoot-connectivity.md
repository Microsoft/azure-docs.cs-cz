---
title: Sledování odpojení a řešení potíží s Azure IoT Hubem
description: Naučte se monitorovat a řešit běžné chyby pomocí připojení zařízení pro Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759606"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorování, diagnostika a řešení potíží s odpojením pomocí služby Azure IoT Hub

Problémy s připojením pro zařízení IoT může být obtížné řešit, protože existuje mnoho možných bodů selhání. Aplikační logika, fyzické sítě, protokoly, hardware, Služby IoT Hub a další cloudové služby mohou způsobit problémy. Schopnost detekovat a určit zdroj problému je kritická. Řešení IoT ve velkém měřítku však může mít tisíce zařízení, takže není praktické kontrolovat jednotlivá zařízení ručně. Abyste tyto problémy ve velkém měřítku pomohli odhalit, diagnostikovat a řešit, použijte funkce monitorování, které služba IoT Hub poskytuje prostřednictvím Služby Azure Monitor. Tyto funkce jsou omezené na to, co služba IoT Hub dokáže sledovat, takže doporučujeme také dodržovat doporučené postupy monitorování pro vaše zařízení a další služby Azure.

## <a name="get-alerts-and-error-logs"></a>Získání upozornění a chybových protokolů

Azure Monitor slouží k získání výstrah a zápisu protokolů při odpojení zařízení.

### <a name="turn-on-diagnostic-logs"></a>Zapnutí diagnostických protokolů

Chcete-li protokolovat události připojení zařízení a chyby, zapněte diagnostiku pro IoT Hub. Doporučujeme zapnout tyto protokoly co nejdříve, protože pokud diagnostické protokoly nejsou povoleny, když dojde k odpojení zařízení, nebudete mít žádné informace k řešení problému.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do svého centra IoT hub.

3. Vyberte **Nastavení diagnostiky**.

4. Vyberte **možnost Zapnout diagnostiku**.

5. Povolit **protokoly připojení,** které mají být shromažďovány.

6. Pro snadnější analýzu zapněte **Odeslat do analýzy protokolů** [(viz ceny).](https://azure.microsoft.com/pricing/details/log-analytics/) Viz příklad v části [Vyřešit chyby připojení](#resolve-connectivity-errors).

   ![Doporučené nastavení](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Další informace najdete [v tématu Sledování stavu služby Azure IoT Hub a diagnostikovat problémy rychle](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Nastavení upozornění na odpojení zařízení ve velkém měřítku

Chcete-li dostávat upozornění při odpojení zařízení, nakonfigurujte výstrahy v metrike **Připojená zařízení (náhled).**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do svého centra IoT hub.

3. Vyberte **možnost Výstrahy**.

4. Vyberte **Nové pravidlo upozornění**.

5. Vyberte **Přidat podmínku**a pak vyberte "Připojená zařízení (náhled)".

6. Nastavte prahovou hodnotu a upozorněte pomocí následujících výzev.

Další informace najdete v tématu [Co jsou výstrahy v Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Detekce jednotlivých odpojení zařízení

Chcete-li zjistit odpojení *podle zařízení,* například když potřebujete vědět, že továrna právě přešla do režimu offline, [nakonfigurujte události odpojení zařízení pomocí služby Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Řešení chyb připojení

Když zapnete diagnostické protokoly a výstrahy pro připojená zařízení, zobrazí se upozornění, když dojde k chybám. Tato část popisuje, jak hledat běžné problémy, když obdržíte výstrahu. Následující kroky předpokládají, že jste nastavili protokoly Azure Monitoru pro diagnostické protokoly.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Přejděte do svého centra IoT hub.

1. Vyberte **Protokoly**.

1. Chcete-li izolovat protokoly chyb připojení pro službu IoT Hub, zadejte následující dotaz a vyberte **Spustit**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Pokud existují výsledky, `OperationName`vyhledejte , `ResultType` (kód chyby) a `ResultDescription` (chybová zpráva) získat další podrobnosti o chybě.

   ![Příklad protokolu chyb](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Postupujte podle pokynů pro řešení problémů pro nejčastější chyby:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 Obecnýčasový osvoje](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Zkoušel jsem kroky, ale nefungovaly

Pokud předchozí kroky nepomohly, zkuste:

* Pokud máte přístup k problematickým zařízením, fyzicky nebo vzdáleně (například SSH), postupujte podle [pokynů pro řešení potíží na straně zařízení](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a pokračujte v řešení potíží.

* Ověřte, že vaše zařízení jsou **povolená** na portálu Azure > vaše služby IoT hub > zařízení IoT.

* Pokud vaše zařízení používá protokol MQTT, ověřte, zda je port 8883 otevřený. Další informace naleznete [v tématu Připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Získejte pomoc od [fóra Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-iot-hub)nebo [podpory Azure](https://azure.microsoft.com/support/options/).

Chcete-li zlepšit dokumentaci pro všechny, zanechte komentář v části zpětné vazby níže, pokud vám tato příručka nepomohla.

## <a name="next-steps"></a>Další kroky

* Další informace o řešení přechodných problémů naleznete [v tématu Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).

* Další informace o Azure IoT SDK a správě opakování najdete v [tématu Správa připojení a spolehlivé zasílání zpráv pomocí sad SDK zařízení Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
