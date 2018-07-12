---
title: Zřízení zařízení Windows pro vzdálené monitorování v jazyce C – Azure | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení k akcelerátoru řešení vzdáleného monitorování, který se pomocí aplikace napsané v jazyce C a systémem Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309866"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Připojení zařízení k akcelerátor řešení vzdálené monitorování (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojení fyzických zařízení k akcelerátoru řešení vzdáleného monitorování.

## <a name="create-a-c-client-solution-on-windows"></a>Tvorba řešení klienta C ve Windows

Stejně jako u nejvíce vložené aplikace, která běží na zařízeních s omezením, kód klienta pro zařízení aplikace napsané v C. V tomto kurzu vytvoříte aplikaci na počítači se systémem Windows.

### <a name="create-the-starter-project"></a>Vytvoření počáteční projekt

Vytvořte počáteční projekt v sadě Visual Studio 2017 a přidání balíčků NuGet klientské zařízení služby IoT Hub:

1. Ve Visual Studiu Vytvořte konzolovou aplikaci jazyka C pomocí Visual C++ **Konzolová aplikace Windows** šablony. Pojmenujte projekt **RMDevice**.

    ![Vytvořte konzolovou aplikaci pro Windows Visual C++](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. V **Průzkumníka řešení**, odstraňte soubory `stdafx.h`, `targetver.h`, a `stdafx.cpp`.

1. V **Průzkumníka řešení**, přejmenujte soubor `RMDevice.cpp` k `RMDevice.c`.

    ![Přejmenovat soubor RMDevice.c zobrazení Průzkumníka řešení](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **RMDevice** projektu a pak klikněte na tlačítko **Správa balíčků NuGet**. Zvolte **Procházet**, vyhledejte a nainstalujte následující balíčky NuGet:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![Správce balíčků NuGet zobrazuje nainstalované balíčky Microsoft.Azure.IoTHub](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **RMDevice** projektu a klikněte na tlačítko **vlastnosti** otevřete v projektu **stránky vlastností** Dialogové okno. Podrobnosti najdete v tématu [nastavení vlastností projektu Visual C++](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Zvolte **C/C++** složky, klikněte na tlačítko **předkompilované hlavičky** stránku vlastností.

1. Nastavte **předkompilovanou hlavičku** k **bez použití předkompilovaných hlaviček**. Klikněte na tlačítko **použít**.

    ![Vlastnosti projektu zobrazit projekt bez použití předkompilovaných hlaviček](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Zvolte **Linkeru** složky, klikněte na tlačítko **vstup** stránku vlastností.

1. Přidat `crypt32.lib` k **Další závislosti** vlastnost. Chcete-li uložit projekt hodnoty vlastností, zvolte **OK** a potom **OK** znovu.

    ![Zobrazit vlastnosti projektu včetně crypt32.lib Linkeru](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Přidání knihovny Parson JSON

Přidat knihovnu do Parson JSON **RMDevice** projekt a přidejte požadované `#include` příkazy:

1. Ve vhodné složce v počítači naklonujte úložiště Parson GitHub pomocí následujícího příkazu:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopírovat `parson.h` a `parson.c` soubory z místní kopie úložiště Parson vaše **RMDevice** složky projektu.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **RMDevice** projektu, zvolte **přidat**a klikněte na tlačítko **existující položku**.

1. V **přidat existující položku** dialogového okna, vyberte `parson.h` a `parson.c` soubory **RMDevice** složky projektu. Chcete-li do projektu přidejte tyto dva soubory, zvolte **přidat**.

    ![Průzkumník řešení zobrazí soubory parson.h a parson.c](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. V sadě Visual Studio, otevřete `RMDevice.c` souboru. Nahraďte existující `#include` příkazy následujícím kódem:

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
    > Teď můžete ověřit, že má váš projekt správnými závislostmi nastavení vytvořením řešení.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Přidejte kód, který má být vyvolán **vzdálené\_monitorování\_spustit** fungovala, pak sestavení a spuštění aplikace zařízení:

1. K vyvolání **vzdálené\_monitorování\_spustit** fungovat, nahraďte **hlavní** funkce s následujícím kódem:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Zvolte **sestavení** a potom **sestavit řešení** k sestavení aplikace pro zařízení.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **RMDevice** projektu, zvolte **ladění**a klikněte na tlačítko **zahájit novou instanci** ke spuštění ukázky . Této konzole se zobrazují zprávy jako:

    * Aplikace odesílá telemetrii ukázka akcelerátor řešení.
    * Získá hodnoty požadované vlastnosti nastavené na řídicím panelu řešení.
    * Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
