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
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175441"
---
## <a name="view-device-telemetry"></a>Zobrazení telemetrie zařízení

Telemetrická data odeslaná ze zařízení můžete zobrazit na stránce **Průzkumník zařízení** v řešení.

1. Vyberte zařízení, které jste zřídit v seznamu zařízení na stránce **Průzkumník zařízení.** Panel zobrazuje informace o vašem zařízení včetně vykreslení telemetrie zařízení:

    ![Viz podrobnosti o zařízení](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Chcete-li změnit zobrazení telemetrie, zvolte **Tlak:**

    ![Zobrazit telemetrii tlaku](media/iot-suite-visualize-connecting/devicespressure.png)

1. Chcete-li zobrazit diagnostické informace o zařízení, přejděte dolů na **Diagnostika**:

    ![Zobrazení diagnostiky zařízení](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Jednejte na svém zařízení

Chcete-li vyvolat metody na vašich zařízeních, použijte stránku **Průzkumník zařízení** v řešení vzdáleného monitorování. Například v řešení vzdáleného **monitorování, zařízení chladiče** implementují metodu **reboot.**

1. Zvolte **Zařízení,** chcete-li přejít na stránku **Průzkumník zařízení** v řešení.

1. Vyberte zařízení, které jste zřídit v seznamu zařízení na stránce **Průzkumník zařízení:**

    ![Vyberte si skutečné zařízení](media/iot-suite-visualize-connecting/devicesselect.png)

1. Chcete-li zobrazit seznam metod, které můžete volat na vašem zařízení, zvolte **Úlohy**, pak **Metody**. Chcete-li naplánovat spuštění úlohy na více zařízeních, můžete v seznamu vybrat více zařízení. Panel **Úlohy** zobrazuje typy metod, které jsou společné pro všechna vybraná zařízení.

1. Zvolte **Restartovat**, nastavte název úlohy na **RebootPhysicalChiller** a pak zvolte **Použít**:

    ![Naplánování aktualizace firmwaru](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Posloupnost zpráv se zobrazí v konzole s kódem zařízení, zatímco simulované zařízení zpracovává metodu.

> [!NOTE]
> Chcete-li sledovat stav úlohy v řešení, zvolte **Zobrazit stav úlohy**.

## <a name="next-steps"></a>Další kroky

V článku [Přizpůsobení akcelerátoru řešení vzdáleného monitorování](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) popisuje některé způsoby přizpůsobení akcelerátoru řešení.