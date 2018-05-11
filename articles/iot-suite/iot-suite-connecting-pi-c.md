---
title: Zřídit malin platformy pro vzdálené monitorování pomocí C - Azure | Microsoft Docs
description: Popisuje, jak se připojit k používání aplikace napsané v c akcelerátoru řešení vzdáleného monitorování malin platformy zařízení
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9de7616ec7174f6c55888a659e9a12bca1e07f94
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Připojení zařízení malin platformy pro vzdálené monitorování akcelerátoru řešení (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Tento kurz ukazuje, jak se připojit ke vzdálené monitorování akcelerátoru řešení fyzického zařízení. Stejně jako u aplikací nejvíce embedded, které jsou spuštěné na omezené zařízení, kód klienta pro aplikaci malin platformy zařízení je napsána v C. V tomto kurzu vytvoříte aplikaci na platformy malin systémem Raspbian operačního systému.

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, abyste mohli vzdáleně připojit k na příkazovém řádku malin pí.

[Microsoft IoT Starter Kit malin pí 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní součásti. V tomto kurzu používá následující položky ze sady kit:

- Malinová pí 3
- Karta MicroSD (s NOOBS)
- Kabelu USB malé
- Kabel Ethernet

### <a name="required-desktop-software"></a>Požadovaný software plochy

Musíte klient SSH na umožňují vzdálený přístup na příkazovém řádku pí malin stolního počítače.

- Windows nezahrnuje klientem SSH. Doporučujeme používat [PuTTY](http://www.putty.org/).
- Většina Linuxových distribucích a Mac OS, obsahují nástroj příkazového řádku SSH. Další informace najdete v tématu [SSH pomocí Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software malin platformy

Tento článek předpokládá, že jste nainstalovali nejnovější verzi [Raspbian operačního systému na vaše malin pí](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Následující kroky ukazují, jak připravit vaše malin platformy pro vytvoření aplikace C, která se připojuje k akcelerátoru řešení:

1. Připojte se k malin pí pomocí **ssh**. Další informace najdete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [web Pi malin](https://www.raspberrypi.org/).

1. Použijte následující příkaz k aktualizaci vašeho malin platformy:

    ```sh
    sudo apt-get update
    ```

1. Pomocí následujícího příkazu přidejte požadované vývojové nástroje a knihovny pro vaše platformy malin:

    ```sh
    sudo apt-get purge libssl-dev
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Použijte následující příkazy ke stažení, sestavit a nainstalovat na vaše platformy malin knihovny klienta služby IoT Hub:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c/build_all/linux
    ./build.sh --no-make
    cd ../../cmake/iotsdk_linux
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Vytvořit projekt

Proveďte následující kroky pomocí **ssh** připojení k vaší malin platformy:

1. Vytvořte složku s názvem `remote_monitoring` do domovské složky na malin pí. Přejděte do této složky ve vašem prostředí:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Vytvořit čtyři soubory **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, a **CMakeLists.txt** v `remote_monitoring` složka.

1. V textovém editoru otevřete **remote_monitoring.c** souboru. Na malin platformy, můžete použít buď **nano** nebo **vi** textový editor. Přidejte následující příkazy `#include`:

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

Uložit **remote_monitoring.c** soubor a ukončete editor.

## <a name="add-code-to-run-the-app"></a>Přidat kód pro spuštění aplikace

V textovém editoru otevřete **remote_monitoring.h** souboru. Přidejte následující kód:

```c
void remote_monitoring_run(void);
```

Uložit **remote_monitoring.h** soubor a ukončete editor.

V textovém editoru otevřete **main.c** souboru. Přidejte následující kód:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

Uložit **main.c** soubor a ukončete editor.

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
      serializer
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. Uložit **CMakeLists.txt** soubor a ukončete editor.

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
