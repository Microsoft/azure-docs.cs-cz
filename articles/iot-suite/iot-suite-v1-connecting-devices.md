---
title: Připojit zařízení s použitím jazyka C ve Windows | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení k Azure IoT Suite předkonfigurované řešení vzdáleného monitorování pomocí aplikace napsané v jazyce C a systémem Windows.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093091"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Připojení zařízení předkonfigurovaného řešení vzdáleného monitorování (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Vytvoření ukázkové řešení C ve Windows
Následující kroky ukazují, jak vytvořit klientskou aplikaci, která komunikuje s předkonfigurovaného řešení vzdáleného monitorování. Tato aplikace je napsaný v jazyce C a vytvořit a spustit v Windows.

Vytvořte počáteční projekt v sadě Visual Studio 2015 nebo Visual Studio 2017 a přidání balíčků NuGet klientské zařízení služby IoT Hub:

1. Ve Visual Studiu Vytvořte konzolovou aplikaci jazyka C pomocí Visual C++ **Konzolová aplikace Win32** šablony. Pojmenujte projekt **RMDevice**.
2. Na **nastavení aplikace** stránku **Průvodce aplikací Win32**, ujistěte se, že **konzolovou aplikaci** je vybraná a zrušte zaškrtnutí políčka **Předkompilovaná hlavička** a **Security Development Lifecycle (SDL) kontroluje**.
3. V **Průzkumníka řešení**, odstraňování souborů stdafx.h, targetver.h a stdafx.cpp.
4. V **Průzkumníka řešení**, přejmenujte soubor RMDevice.cpp RMDevice.c.
5. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **RMDevice** projektu a pak klikněte na tlačítko **Správa balíčků NuGet**. Klikněte na tlačítko **Procházet**, vyhledejte a nainstalujte následující balíčky NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **RMDevice** projektu a pak klikněte na tlačítko **vlastnosti** otevřete v projektu **stránky vlastností** Dialogové okno. Podrobnosti najdete v tématu [nastavení vlastností projektu Visual C++][lnk-c-project-properties]. 
7. Klikněte na tlačítko **Linkeru** složky, klikněte **vstup** stránku vlastností.
8. Přidat **crypt32.lib** k **Další závislosti** vlastnost. Klikněte na tlačítko **OK** a potom **OK** znovu pro uložení hodnoty vlastnosti projektu.

Přidat knihovnu do Parson JSON **RMDevice** projekt a přidejte požadované `#include` příkazy:

1. Ve vhodné složce v počítači naklonujte úložiště Parson GitHub pomocí následujícího příkazu:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Zkopírujte soubory parson.h a parson.c z místní kopie úložiště Parson vaše **RMDevice** složky projektu.

1. V sadě Visual Studio, klikněte pravým tlačítkem myši **RMDevice** projektu, klikněte na tlačítko **přidat**a potom klikněte na **existující položku**.

1. V **přidat existující položku** dialogovém okně vyberte parson.h a parson.c soubory **RMDevice** složky projektu. Pak klikněte na tlačítko **přidat** přidat tyto dva soubory do projektu.

1. V sadě Visual Studio otevřete soubor RMDevice.c. Nahraďte existující `#include` příkazy následujícím kódem:
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Teď můžete ověřit, že má váš projekt správnými závislostmi nastavit podle jeho sestavení.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Přidejte kód, který má být vyvolán **vzdálené\_monitorování\_spustit** funkce a potom sestavíte a spustíte aplikaci pro zařízení.

1. Nahraďte **hlavní** funkce s následujícím kódem, který má být vyvolán **vzdálené\_monitorování\_spustit** funkce:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Klikněte na tlačítko **sestavení** a potom **sestavit řešení** k sestavení aplikace pro zařízení.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **RMDevice** projektu, klikněte na tlačítko **ladění**a potom klikněte na **zahájit novou instanci** ke spuštění ukázky. Této konzole se zobrazují zprávy jako aplikace odesílá ukázková telemetrická data do předkonfigurovaného řešení, hodnoty požadované vlastnosti nastavené na řídicím panelu řešení přijímá a reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
