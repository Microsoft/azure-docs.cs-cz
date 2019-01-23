---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 73ba80878615f04e1755a4d12014691c5ae2a077
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453116"
---
## <a name="view-device-telemetry"></a>Zobrazení telemetrie zařízení

Můžete zobrazit telemetrická data odesílaná ze zařízení **zařízení** stránku v řešení.

1. Vyberte zařízení, které jsou zřízené v seznamu zařízení na **zařízení** stránky. Panelu se zobrazí informace o vašem zařízení, včetně diagramů telemetrie zařízení:

    ![Viz podrobnosti o zařízení](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Zvolte **tlak** ke změně zobrazení telemetrických dat:

    ![Zobrazení telemetrie tlaku](media/iot-suite-visualize-connecting/devicespressure.png)

1. Chcete-li zobrazit diagnostické informace o zařízení, přejděte dolů k položce **diagnostiky**:

    ![Zobrazit diagnostiku zařízení](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Reagovat na vaše zařízení

Chcete-li vyvolávat metody v zařízení, použijte **zařízení** stránku v řešení vzdáleného monitorování. Například v řešení vzdáleného monitorování **chladič** zařízení implementovat **FirmwareUpdate** metody.

1. Zvolte **zařízení** přejděte **zařízení** stránku v řešení.

1. Vyberte zařízení, které jsou zřízené v seznamu zařízení na **zařízení** stránky:

    ![Vyberte skutečných zařízení](media/iot-suite-visualize-connecting/devicesselect.png)

1. Chcete-li zobrazit seznam metod, můžete volat na vašem zařízení, zvolte **úlohy**, pak **spustit metodu**. Naplánování úlohy spouštět na více zařízeních, můžete vybrat několik zařízení v seznamu. **Úlohy** panelu se zobrazí typy metoda společná pro všechna zařízení, která jste vybrali.

1. Zvolte **FirmwareUpdate**, nastavte název úlohy **UpdatePhysicalChiller**. Nastavte **verze firmwaru** k **2.0.0**, nastavte **Firmware URI** k **http://contoso.com/updates/firmware.bin**a klikněte na tlačítko **použít**:

    ![Plán aktualizace firmwaru](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Pořadí zpráv se zobrazí v konzole spuštěný kód vašeho zařízení, když Simulovaná zařízení zpracovává tuto metodu.

1. Po dokončení aktualizace se zobrazí nová verze firmwaru na **zařízení** stránky:

    ![Aktualizace dokončena](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Chcete-li sledovat stav úlohy v řešení, zvolte **zobrazení**.

## <a name="next-steps"></a>Další postup

Tento článek [přizpůsobení akcelerátoru řešení vzdáleného monitorování](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) popisuje některé způsoby, jak přizpůsobení akcelerátoru řešení.