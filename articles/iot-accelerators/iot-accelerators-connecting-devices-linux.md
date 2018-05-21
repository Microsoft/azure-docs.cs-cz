---
title: Zřídit Linux zařízení pro vzdálené monitorování v jazyce C - Azure | Microsoft Docs
description: Popisuje, jak se připojit zařízení k používání aplikace napsané v jazyce C systémem Linux akcelerátoru řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9ccf2b185c5aa67b41aab650989ad0326aba0215
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Připojte zařízení k vzdálené monitorování akcelerátoru řešení (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Tento kurz ukazuje, jak se připojit ke vzdálené monitorování akcelerátoru řešení fyzického zařízení.

## <a name="create-a-c-client-project-on-linux"></a>Vytvoření projektu C klienta v systému Linux

Stejně jako u aplikací nejvíce embedded, které jsou spuštěné na omezené zařízení, kód klienta pro aplikaci zařízení je napsána v C. V tomto kurzu vytvoříte aplikaci na počítači se systémem Ubuntu (Linux).

K provedení těchto kroků, je třeba zařízení se systémem Ubuntu verze 15.04 nebo novější. Než budete pokračovat, nainstalujte požadované balíčky zařízení Ubuntu pomocí následujícího příkazu:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Nainstalujte klientské knihovny zařízení

Knihovny klienta Azure IoT Hub jsou k dispozici jako balíček můžete nainstalovat pomocí zařízení Ubuntu **výstižný get** příkaz. Proveďte následující kroky k instalaci balíčku, který obsahuje IoT Hub knihovny a hlavičky souborů klienta v počítači Ubuntu:

1. V prostředí přidejte do počítače AzureIoT úložiště:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Nainstalovat balíček azure-iot-sdk-c vývojářů

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Nainstalujte analyzátor Parson JSON

Knihovny klienta služby IoT Hub pomocí analyzátoru Parson JSON analyzovat datové části zprávy. Ve složce vhodný ve vašem počítači naklonujte úložiště Parson GitHub pomocí následujícího příkazu:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Příprava projektu

Na počítači Ubuntu, vytvořte složku s názvem `remote_monitoring`. V `remote_monitoring` složky:

- Vytvořit čtyři soubory `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, a `CMakeLists.txt`.
- Vytvořte složku s názvem `parson`.

Zkopírujte soubory `parson.c` a `parson.h` z místní kopie Parson úložiště do `remote_monitoring/parson` složky.

V textovém editoru otevřete `remote_monitoring.c` souboru. Přidejte následující příkazy `#include`:

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

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Přidat kód pro spuštění aplikace

V textovém editoru otevřete `remote_monitoring.h` souboru. Přidejte následující kód:

```c
void remote_monitoring_run(void);
```

V textovém editoru otevřete `main.c` souboru. Přidejte následující kód:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Následující kroky popisují způsob použití *CMake* k vytvoření klientské aplikace.

1. V textovém editoru otevřete **CMakeLists.txt** v soubor `remote_monitoring` složky.

1. Přidejte podle následujících pokynů můžete definovat, jak vytvořit klientskou aplikaci:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. V `remote_monitoring` složky, vytvořte složku pro uložení *zkontrolujte* soubory, které generuje CMake. Spusťte **cmake** a **zkontrolujte** příkazy následujícím způsobem:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Spusťte aplikaci klienta a odesílat telemetrická data do služby IoT Hub:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
