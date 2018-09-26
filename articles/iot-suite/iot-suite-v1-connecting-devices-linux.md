---
title: Připojit zařízení s použitím jazyka C v Linuxu | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení k Azure IoT Suite předkonfigurované řešení vzdáleného monitorování pomocí aplikace napsané v jazyce C běžící na Linuxu.
services: ''
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094514"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Připojení zařízení předkonfigurovaného řešení vzdáleného monitorování (Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Sestavte a spusťte ukázkové C klienta systému Linux
Následující kroky ukazují, jak vytvořit klientskou aplikaci, která komunikuje s předkonfigurovaného řešení vzdáleného monitorování. Tato aplikace je napsaný v jazyce C a vytvořit a spustit v Ubuntu Linux.

K dokončení těchto kroků, je potřeba zařízení se systémem Ubuntu verze 15.04 nebo 15.10. Než budete pokračovat, nainstalujte požadované balíčky na vašem zařízení se systémem Ubuntu pomocí následujícího příkazu:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Nainstalujte klientské knihovny na vašem zařízení
Jsou k dispozici jako balíček můžete nainstalovat na vaše zařízení se systémem Ubuntu pomocí klientských knihoven Azure IoT Hub **apt-get** příkazu. Proveďte následující kroky k instalaci balíčku, který obsahuje službu IoT Hub knihovny a hlavičky souborů klienta v počítači se systémem Ubuntu:

1. V prostředí přidejte do počítače AzureIoT úložiště:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Instalace balíčku azure-iot-sdk-c-dev
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Nainstalujte analyzátor Parson JSON
Tyto klientské knihovny služby IoT Hub použít k analýze datové části zprávy analyzátor Parson JSON. Ve vhodné složce v počítači naklonujte úložiště Parson GitHub pomocí následujícího příkazu:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Příprava projektu
Na počítači s Ubuntu, vytvořte složku s názvem **vzdálené\_monitorování**. V **vzdálené\_monitorování** složky:

- Vytvořit čtyři soubory **main.c**, **vzdálené\_monitoring.c**, **vzdálené\_monitoring.h**, a **CMakeLists.txt**.
- Vytvořte složku s názvem **parson**.

Zkopírujte soubory **parson.c** a **parson.h** z místní kopie úložiště Parson do **vzdálené\_monitorování/parson** složky.

V textovém editoru otevřete **vzdálené\_monitoring.c** souboru. Přidejte následující příkazy `#include`:
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Volání vzdáleného\_monitorování\_run – funkce
V textovém editoru otevřete **remote_monitoring.h** souboru. Přidejte následující kód:

```
void remote_monitoring_run(void);
```

V textovém editoru otevřete **main.c** souboru. Přidejte následující kód:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace
Následující kroky popisují způsob použití *CMake* k vytvoření klientské aplikace.

1. V textovém editoru otevřete **CMakeLists.txt** soubor **remote_monitoring** složky.

1. Přidejte následující pokyny k definování, jak vytvořit klientskou aplikaci:
   
    ```
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
1. V **remote_monitoring** složce vytvořte složku pro uložení *Ujistěte se,* soubory, které generuje CMake a potom spusťte **cmake** a **Ujistěte se,** Následující příkazy:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Spuštění klientské aplikace a odesílání telemetrických dat do služby IoT Hub:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

