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
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56988074"
---
## <a name="view-device-telemetry"></a>Zobrazení telemetrie zařízení

Můžete zobrazit telemetrická data odesílaná ze zařízení **Device Explorer** stránku v řešení.

1. Vyberte zařízení, které jsou zřízené v seznamu zařízení na **Device Explorer** stránky. Panelu se zobrazí informace o vašem zařízení, včetně diagramů telemetrie zařízení:

    ![Viz podrobnosti o zařízení](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Zvolte **tlak** ke změně zobrazení telemetrických dat:

    ![Zobrazení telemetrie tlaku](media/iot-suite-visualize-connecting/devicespressure.png)

1. Chcete-li zobrazit diagnostické informace o zařízení, přejděte dolů k položce **diagnostiky**:

    ![Zobrazit diagnostiku zařízení](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Reagovat na vaše zařízení

Chcete-li vyvolávat metody v zařízení, použijte **Device Explorer** stránku v řešení vzdáleného monitorování. Například v řešení vzdáleného monitorování **chladič** zařízení implementovat **restartování** metoda.

1. Zvolte **zařízení** přejděte **Device Explorer** stránku v řešení.

1. Vyberte zařízení, které jsou zřízené v seznamu zařízení na **Device Explorer** stránky:

    ![Vyberte skutečných zařízení](media/iot-suite-visualize-connecting/devicesselect.png)

1. Chcete-li zobrazit seznam metod, můžete volat na vašem zařízení, zvolte **úlohy**, pak **metody**. Naplánování úlohy spouštět na více zařízeních, můžete vybrat několik zařízení v seznamu. **Úlohy** panelu se zobrazí typy metoda společná pro všechna zařízení, která jste vybrali.

1. Zvolte **restartovat**, nastavte název úlohy **RebootPhysicalChiller** a klikněte na tlačítko **použít**:

    ![Plán aktualizace firmwaru](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Pořadí zpráv se zobrazí v konzole spuštěný kód vašeho zařízení, když Simulovaná zařízení zpracovává tuto metodu.

> [!NOTE]
> Chcete-li sledovat stav úlohy v řešení, zvolte **zobrazení stavu úlohy**.

## <a name="next-steps"></a>Další postup

Tento článek [přizpůsobení akcelerátoru řešení vzdáleného monitorování](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) popisuje některé způsoby, jak přizpůsobení akcelerátoru řešení.