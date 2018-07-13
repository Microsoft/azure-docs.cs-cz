---
title: Zřízení Raspberry Pi pro vzdálené monitorování pomocí jazyka C – Azure | Dokumentace Microsoftu
description: Popisuje, jak připojit Raspberry Pi zařízení pomocí aplikace napsané v C. akcelerátor řešení vzdálené monitorování
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 23e84a8d577bb1c4950de3acd76b0f8528551ae0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611437"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Připojte Raspberry Pi zařízení k akcelerátor řešení vzdálené monitorování (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojení fyzických zařízení k akcelerátoru řešení vzdáleného monitorování. Stejně jako u nejvíce vložené aplikace, která běží na zařízeních s omezením, kód klienta pro aplikaci zařízení Raspberry Pi napsané v C. V tomto kurzu vytvoříte aplikaci na Raspberry Pi Raspbian operačním systémem.

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, která umožňuje vzdáleně připojit k příkazovému řádku na Raspberry Pi.

[Microsoft IoT Starter Kit Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní součásti. V tomto kurzu používá následující položky ze sady:

- Raspberry Pi 3
- Karta MicroSD (s NOOBS)
- USB Mini kabel
- Kabelu Ethernet

### <a name="required-desktop-software"></a>Požadované desktopového softwaru

Na desktopovém počítači povolit vzdálený přístup k příkazového řádku na Raspberry Pi musíte klienta SSH.

- Windows nezahrnuje klienta SSH. Doporučujeme používat [PuTTY](http://www.putty.org/).
- Většině distribucí systému Linux, Mac OS zahrnují příkazový řádek SSH. Další informace najdete v tématu [SSH pomocí systému Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software Raspberry Pi

Tento článek předpokládá, že máte nainstalovanou nejnovější verzi z [Raspbian operační systém na Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Následující kroky ukazují, jak připravit Raspberry Pi pro vytváření aplikace v jazyce C, která se připojuje k akcelerátoru řešení:

1. Připojte se k Raspberry Pi pomocí **ssh**. Další informace najdete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [webu Raspberry Pi](https://www.raspberrypi.org/).

1. Chcete-li aktualizovat Raspberry Pi, použijte následující příkaz:

    ```sh
    sudo apt-get update
    ```

1. Chcete-li přidat požadované vývojářské nástroje a knihovny pro Raspberry Pi, použijte následující příkaz:

    ```sh
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Stáhnout, sestavit a nainstalovat klientské knihovny pro centra IoT na Raspberry Pi, použijte následující příkazy:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    mkdir cmake
    cd cmake
    cmake ..
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Vytvoření projektu

Proveďte následující kroky pomocí **ssh** připojení na Raspberry Pi:

1. Vytvořte složku s názvem `remote_monitoring` ve vaší domovské složky na Raspberry Pi. Přejděte do této složky ve vašem prostředí:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Vytvořit čtyři soubory **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, a **CMakeLists.txt** v `remote_monitoring` složka.

1. V textovém editoru otevřete **remote_monitoring.c** souboru. Na Raspberry Pi, můžete použít buď **nano** nebo **vi** textového editoru. Přidejte následující příkazy `#include`:

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

## <a name="add-code-to-run-the-app"></a>Přidejte kód pro spuštění aplikace

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
