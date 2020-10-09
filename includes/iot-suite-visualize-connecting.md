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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175441"
---
## <a name="view-device-telemetry"></a>Zobrazení telemetrie zařízení

Telemetrii odeslanou ze zařízení můžete zobrazit na stránce **Device Explorer** v řešení.

1. Vyberte zařízení, které jste zřídili v seznamu zařízení na stránce **Device Explorer** . Na panelu se zobrazí informace o zařízení, včetně grafu telemetrie zařízení:

    ![Zobrazit podrobnosti o zařízení](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Vyberte možnost **přítlak** a změňte zobrazení telemetrie:

    ![Zobrazit telemetrii tlaků](media/iot-suite-visualize-connecting/devicespressure.png)

1. Chcete-li zobrazit diagnostické informace o zařízení, přejděte dolů k **diagnostice**:

    ![Zobrazení diagnostiky zařízení](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Fungování na zařízení

K vyvolání metod v zařízeních použijte stránku **Device Explorer** v řešení vzdáleného monitorování. Například v zařízeních **chlazení** řešení vzdáleného monitorování implementují metodu **restartování** .

1. Vyberte **zařízení** a přejděte na stránku **Device Explorer** v řešení.

1. Vyberte zařízení, které jste zřídili v seznamu zařízení na stránce **Device Explorer** :

    ![Výběr reálného zařízení](media/iot-suite-visualize-connecting/devicesselect.png)

1. Chcete-li zobrazit seznam metod, které můžete volat na zařízení, zvolte **úlohy**a pak **metody**. Pokud chcete naplánovat spuštění úlohy na více zařízeních, můžete v seznamu vybrat více zařízení. Panel **úlohy** zobrazuje typy metod společných pro všechna zařízení, která jste vybrali.

1. Zvolte **restartovat**, nastavte název úlohy na **RebootPhysicalChiller** a pak zvolte **použít**:

    ![Naplánovat aktualizaci firmwaru](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Posloupnost zpráv se zobrazí v konzole spouštějící kód vašeho zařízení, zatímco simulované zařízení zpracovává tuto metodu.

> [!NOTE]
> Chcete-li sledovat stav úlohy v řešení, vyberte možnost **Zobrazit stav úlohy**.

## <a name="next-steps"></a>Další kroky

V článku [přizpůsobení akcelerátoru řešení vzdáleného monitorování](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) se dozvíte několik způsobů přizpůsobení akcelerátoru řešení.