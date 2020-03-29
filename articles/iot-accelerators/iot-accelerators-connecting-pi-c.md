---
title: Zřízení Raspberry Pi vzdálenému monitorování pomocí C - Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak připojit zařízení Raspberry Pi k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454491"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Připojte zařízení Raspberry Pi k akcelerátoru řešení vzdáleného monitorování (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se můžete připojit skutečné zařízení k akcelerátoru řešení vzdáleného monitorování. Stejně jako u většiny vestavěných aplikací, které běží na omezených zařízeních, je klientský kód pro aplikaci zařízení Raspberry Pi napsán v C. V tomto kurzu vytvoříte aplikaci na Raspberry Pi se systémem Raspbian OS.

Pokud dáváte přednost simulaci zařízení, přečtěte si informace [o vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, který vám umožní vzdáleně se připojit k příkazovému řádku na Raspberry Pi.

[Microsoft IoT Starter Kit pro Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní komponenty. Tento kurz používá následující položky ze sady:

- Malina Pi 3
- MicroSD karta (s NOOBS)
- Kabel USB Mini
- Ethernetový kabel

### <a name="required-desktop-software"></a>Požadovaný software pro stolní počítače

Potřebujete klienta SSH na vašem stolním počítači, abyste mohli vzdáleně přistupovat k příkazovému řádku na Raspberry Pi.

- Systém Windows neobsahuje klienta SSH. Doporučujeme používat [PuTTY](https://www.putty.org/).
- Většina linuxových distribucí a Mac OS obsahuje nástroj SSH příkazového řádku. Další informace naleznete v tématu [SSH Using Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software Raspberry Pi

Tento článek předpokládá, že jste nainstalovali nejnovější verzi [operačního systému Raspbian na raspberry pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Následující kroky ukazují, jak připravit Raspberry Pi pro vytvoření aplikace C, která se připojuje k akcelerátoru řešení:

1. Připojte se k raspberry pi pomocí **ssh**. Další informace naleznete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [webových stránkách Raspberry Pi](https://www.raspberrypi.org/).

1. Pomocí následujícího příkazu aktualizujte Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Chcete-li dokončit kroky v tomto návodu, postupujte podle pokynů v [nastavení vývojového prostředí Linuxu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) a přidejte požadované vývojové nástroje a knihovny do raspberry pi.

## <a name="view-the-code"></a>Zobrazit kód

[Ukázkový kód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) použitý v této příručce je k dispozici v úložišti GitHub Azure IoT C SDKs.

### <a name="download-the-source-code-and-prepare-the-project"></a>Stáhněte si zdrojový kód a připravte projekt

Chcete-li připravit projekt, klonujte nebo stáhněte [úložiště Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) z GitHubu.

Vzorek je umístěn ve složce **vzorky/řešení/remote_monitoring_client.**

Otevřete soubor **remote_monitoring.c** ve složce **samples/solutions/remote_monitoring_client** v textovém editoru.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Následující kroky popisují, jak pomocí *CMake* vytvořit klientskou aplikaci. Klientská aplikace vzdáleného monitorování je vytvořena jako součást procesu sestavení sady SDK.

1. Upravte soubor **remote_monitoring.c,** který chcete nahradit `<connectionstring>` připojovacím řetězcem zařízení, který jste si poznamenali na začátku tohoto návodu, když jste přidali zařízení do akcelerátoru řešení.

1. Přejděte do kořenového adresáře klonované kopie [úložiště úložiště sad Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a spusťte následující příkazy k vytvoření klientské aplikace:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Spusťte klientskou aplikaci a odešlete telemetrii do služby IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Konzole zobrazuje zprávy jako:

    - Aplikace odešle ukázkovou telemetrii do akcelerátoru řešení.
    - Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
