---
title: Zřízení zařízení se systémem Windows pro vzdálené monitorování v jazyce C - Azure | Microsoft Docs
description: Popisuje, jak se připojit zařízení k řešení akcelerátoru vzdálené monitorování pomocí aplikace napsané v jazyce C v systému Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626951"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Připojte zařízení k řešení akcelerátoru vzdálené monitorování (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak se připojit fyzického zařízení k akcelerátoru řešení vzdáleného monitorování.

## <a name="create-a-c-client-solution-on-windows"></a>Vytvořte klienta řešení C v systému Windows

Stejně jako u aplikací nejvíce embedded, které jsou spuštěné na omezené zařízení, kód klienta pro aplikaci zařízení je napsána v C. V tomto kurzu vytvoříte aplikaci na počítači se systémem Windows.

### <a name="create-the-starter-project"></a>Vytvoření projektu starter

Vytvoření projektu starter v Visual Studio 2017 a přidání balíčků NuGet IoT Hub zařízení klienta:

1. Ve Visual Studiu Vytvořte konzolovou aplikaci C pomocí Visual C++ **konzolové aplikace pro Windows** šablony. Název projektu **RMDevice**.

    ![Vytvoření konzolové aplikace pro Visual C++ Windows](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. V **Průzkumníku řešení**, odstraňte soubory `stdafx.h`, `targetver.h`, a `stdafx.cpp`.

1. V **Průzkumníku řešení**, přejmenujte soubor `RMDevice.cpp` k `RMDevice.c`.

    ![Zobrazuje Průzkumníka řešení přejmenovat soubor RMDevice.c](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **RMDevice** projektu a pak klikněte na **spravovat balíčky NuGet balíčky**. Zvolte **Procházet**, vyhledejte a nainstalujte následující balíčky NuGet:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![Správce balíčků NuGet zobrazuje nainstalované balíčky Microsoft.Azure.IoTHub](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **RMDevice** projektu a potom zvolte **vlastnosti** otevření projektu **stránky vlastností** Dialogové okno. Podrobnosti najdete v tématu [nastavení vlastností projektu Visual C++](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Vyberte **C/C++** složky, vyberte **předkompilovaných hlaviček** stránku vlastností.

1. Nastavit **předkompilované hlavičky** k **bez použití předkompilovaných hlaviček**. Zvolte **použít**.

    ![Vlastnosti projektu zobrazit projektu není použití předkompilovaných hlaviček](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Vyberte **Linkeru** složky, vyberte **vstup** stránku vlastností.

1. Přidat `crypt32.lib` k **Další závislosti** vlastnost. Pokud chcete uložit projekt hodnoty vlastností, zvolte **OK** a potom **OK** znovu.

    ![Vlastnosti projektu zobrazit včetně crypt32.lib Linkeru](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Přidání knihovny Parson JSON

Přidat Parson JSON knihovnu, která má **RMDevice** projekt a přidejte požadované `#include` příkazy:

1. Ve složce vhodný ve vašem počítači naklonujte úložiště Parson GitHub pomocí následujícího příkazu:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopírování `parson.h` a `parson.c` soubory z místní kopie Parson úložiště k vaší **RMDevice** složce projektu.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **RMDevice** projektu, zvolte **přidat**a potom zvolte **existující položka**.

1. V **přidat existující položku** dialogovém okně, vyberte `parson.h` a `parson.c` soubory **RMDevice** složce projektu. Chcete-li do projektu přidejte tyto dva soubory, zvolte **přidat**.

    ![Průzkumník řešení zobrazuje parson.h a parson.c souborů](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. V sadě Visual Studio, otevřete `RMDevice.c` souboru. Nahradit existující `#include` příkazy následujícím kódem:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > Nyní můžete ověřit, že váš projekt má správnými závislostmi nastavit tak, že sestavení řešení.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Přidejte kód, který má být vyvolán **vzdáleného\_monitorování\_spustit** fungovat, pak sestavit a spustit aplikaci zařízení:

1. Má být vyvolán **vzdáleného\_monitorování\_spustit** fungovat, nahraďte **hlavní** funkce s následujícím kódem:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Zvolte **sestavení** a potom **sestavit řešení** sestavit aplikaci pro zařízení.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **RMDevice** projektu, zvolte **ladění**a potom zvolte **spustit novou instanci** ke spuštění ukázky . Konzole zobrazí zprávy jako:

    * Aplikace odesílá telemetrii ukázka na akcelerátoru řešení.
    * Přijme požadovanou vlastnost hodnotami nastavenými na řídicím panelu řešení.
    * Odpoví na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
