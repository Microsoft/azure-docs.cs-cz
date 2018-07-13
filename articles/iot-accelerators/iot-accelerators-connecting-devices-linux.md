---
title: Zřízení zařízení pro Linux pro vzdálené monitorování v jazyce C – Azure | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení pomocí aplikace napsané v jazyce C v Linuxu spuštěnou akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 5d7d6522dc663f13ce40cc638ba90ac4043d435c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611408"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Připojení zařízení k akcelerátor řešení vzdálené monitorování (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojení fyzických zařízení k akcelerátoru řešení vzdáleného monitorování.

## <a name="create-a-c-client-project-on-linux"></a>Vytvoření projektu klienta C v Linuxu

Stejně jako u nejvíce vložené aplikace, která běží na zařízeních s omezením, kód klienta pro zařízení aplikace napsané v C. V tomto kurzu vytvoříte aplikaci na počítači s Ubuntu (Linux).

K dokončení těchto kroků, je třeba zařízení se systémem Ubuntu verze 15.04 nebo novější. Než budete pokračovat, nainstalujte požadované balíčky na vašem zařízení se systémem Ubuntu pomocí následujícího příkazu:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Nainstalujte klientské knihovny na vašem zařízení

Jsou k dispozici jako balíček můžete nainstalovat na vaše zařízení se systémem Ubuntu pomocí klientských knihoven Azure IoT Hub **apt-get** příkazu. Proveďte následující kroky k instalaci balíčku, který obsahuje službu IoT Hub knihovny a hlavičky souborů klienta v počítači se systémem Ubuntu:

1. V prostředí přidejte do počítače AzureIoT úložiště:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Instalace balíčku azure-iot-sdk-c-dev

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Nainstalujte analyzátor Parson JSON

Tyto klientské knihovny služby IoT Hub použít k analýze datové části zprávy analyzátor Parson JSON. Ve vhodné složce v počítači naklonujte úložiště Parson GitHub pomocí následujícího příkazu:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Příprava projektu

Na počítači s Ubuntu, vytvořte složku s názvem `remote_monitoring`. V `remote_monitoring` složky:

- Vytvořit čtyři soubory `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, a `CMakeLists.txt`.
- Vytvořte složku s názvem `parson`.

Zkopírujte soubory `parson.c` a `parson.h` z místní kopie úložiště Parson do `remote_monitoring/parson` složky.

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

## <a name="add-code-to-run-the-app"></a>Přidejte kód pro spuštění aplikace

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

1. V textovém editoru otevřete **CMakeLists.txt** soubor `remote_monitoring` složky.

1. Přidejte následující pokyny k definování, jak vytvořit klientskou aplikaci:

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

1. V `remote_monitoring` složce vytvořte složku pro uložení *Ujistěte se,* soubory, které generuje CMake. Spusťte **cmake** a **zkontrolujte** příkazy následujícím způsobem:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Spuštění klientské aplikace a odesílání telemetrických dat do služby IoT Hub:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
