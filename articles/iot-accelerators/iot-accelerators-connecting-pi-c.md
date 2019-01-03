---
title: Zřízení Raspberry Pi pro vzdálené monitorování pomocí jazyka C – Azure | Dokumentace Microsoftu
description: Popisuje, jak připojit Raspberry Pi zařízení pomocí aplikace napsané v C. akcelerátor řešení vzdálené monitorování
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 838a33fd390b28fec609c42487dca225ddc6eaa8
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628200"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Připojte Raspberry Pi zařízení k akcelerátor řešení vzdálené monitorování (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak se připojit skutečné zařízení k akcelerátoru řešení vzdáleného monitorování. Stejně jako u nejvíce vložené aplikace, která běží na zařízeních s omezením, kód klienta pro aplikaci zařízení Raspberry Pi napsané v C. V tomto kurzu vytvoříte aplikaci na Raspberry Pi Raspbian operačním systémem.

Pokud chcete simulovat zařízení, přečtěte si téma [vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, která umožňuje vzdáleně připojit k příkazovému řádku na Raspberry Pi.

[Microsoft IoT Starter Kit Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní součásti. V tomto kurzu používá následující položky ze sady:

- Raspberry Pi 3
- Karta MicroSD (s NOOBS)
- USB Mini kabel
- Kabelu Ethernet

### <a name="required-desktop-software"></a>Požadované desktopového softwaru

Na desktopovém počítači povolit vzdálený přístup k příkazového řádku na Raspberry Pi musíte klienta SSH.

- Windows nezahrnuje klienta SSH. Doporučujeme používat [PuTTY](https://www.putty.org/).
- Většině distribucí systému Linux, Mac OS zahrnují příkazový řádek SSH. Další informace najdete v tématu [SSH pomocí systému Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software Raspberry Pi

Tento článek předpokládá, že máte nainstalovanou nejnovější verzi z [Raspbian operační systém na Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Následující kroky ukazují, jak připravit Raspberry Pi pro vytváření aplikace v jazyce C, která se připojuje k akcelerátoru řešení:

1. Připojte se k Raspberry Pi pomocí **ssh**. Další informace najdete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [webu Raspberry Pi](https://www.raspberrypi.org/).

1. Chcete-li aktualizovat Raspberry Pi, použijte následující příkaz:

    ```sh
    sudo apt-get update
    ```

1. K dokončení kroků v této příručce s postupy, postupujte podle kroků v [nastavení vývojového prostředí Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) doplnit Raspberry Pi požadované vývojářské nástroje a knihovny.

## <a name="view-the-code"></a>Zobrazení kódu

[Ukázkový kód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) použitý v tomto průvodci je k dispozici v úložišti Azure IoT C SDK na Githubu.

### <a name="download-the-source-code-and-prepare-the-project"></a>Stáhněte si zdrojový kód a příprava projektu

Příprava projektu, klonovat nebo stáhnout [úložiště sad SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) z Githubu.

Tato ukázka se nachází v **ukázky a řešení/remote_monitoring_client** složky.

Otevřít **remote_monitoring.c** soubor **ukázky a řešení/remote_monitoring_client** složky v textovém editoru.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Následující kroky popisují způsob použití *CMake* k vytvoření klientské aplikace. Vzdálené monitorování klientské aplikace je vytvořený jako součást procesu sestavení sady SDK.

1. Upravit **remote_monitoring.c** souboru nahraďte `<connectionstring>` připojovacím řetězcem zařízení, které jste si poznamenali na začátku příručky s postupy po přidání zařízení do akcelerátoru řešení.

1. Přejděte do kořenového adresáře naklonované kopie [úložiště sad SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) úložiště a spusťte následující příkazy k vytvoření klientské aplikace:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Spuštění klientské aplikace a odesílání telemetrických dat do služby IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Této konzole se zobrazují zprávy jako:

    - Aplikace odesílá telemetrii ukázka akcelerátor řešení.
    - Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
