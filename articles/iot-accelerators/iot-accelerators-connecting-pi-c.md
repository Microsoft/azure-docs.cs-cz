---
title: Zřízení maliny PI pro vzdálené monitorování pomocí jazyka C – Azure | Microsoft Docs
description: V této části najdete popis postupu připojení zařízení malinu pi k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v jazyce C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454491"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Připojení zařízení malinu pi k akcelerátoru řešení vzdáleného monitorování (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojit reálné zařízení k akcelerátoru řešení vzdáleného monitorování. Stejně jako u většiny vložených aplikací, které běží na omezených zařízeních, je kód klienta pro aplikaci zařízení maliny PI napsán v jazyce C. V tomto kurzu sestavíte aplikaci na Malině PI s běžícím operačním systémem Raspbian.

Pokud dáváte přednost simulaci zařízení, přečtěte si téma [Vytvoření a otestování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Požadovaný hardware

Stolní počítač, který vám umožní vzdálené připojení k příkazovému řádku v malin. PI.

[Sada Microsoft IoT Starter Kit pro malinu PI 3](https://azure.microsoft.com/develop/iot/starter-kits/) nebo ekvivalentní součásti. Tento kurz používá následující položky ze sady:

- Malina Pi 3
- Karta MicroSD (s NOOBS)
- Mini kabel USB
- Kabel Ethernet

### <a name="required-desktop-software"></a>Požadovaný desktopový software

Na stolním počítači budete potřebovat klienta SSH, aby bylo možné vzdáleně přistupovat k příkazovému řádku v malinu PI.

- Systém Windows nezahrnuje klienta SSH. Doporučujeme použít k [disvýstupu](https://www.putty.org/).
- Většina distribucí a Mac OS systému Linux zahrnuje nástroj SSH nástroje příkazového řádku. Další informace najdete v tématu [SSH pomocí systému Linux nebo Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Požadovaný software maliny PI

V tomto článku se předpokládá, že jste nainstalovali nejnovější verzi [RASPBIAN OS do malin. PI](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Následující kroky ukazují, jak připravit aplikaci malin PI pro vytváření aplikací v jazyce C, které se připojují k akcelerátoru řešení:

1. Připojte se k malin. PI pomocí **SSH**. Další informace najdete v tématu [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [webu maliny PI](https://www.raspberrypi.org/).

1. Pomocí následujícího příkazu aktualizujte aplikaci malin PI:

    ```sh
    sudo apt-get update
    ```

1. Kroky v tomto průvodci návodem vám pomohou postupovat podle kroků v tématu [Nastavení vývojového prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) , aby se do maliny PI přidaly požadované vývojové nástroje a knihovny.

## <a name="view-the-code"></a>Zobrazit kód

[Vzorový kód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) použitý v tomto průvodci je k dispozici v úložišti GitHub pro Azure IoT C SDK.

### <a name="download-the-source-code-and-prepare-the-project"></a>Stažení zdrojového kódu a příprava projektu

Pokud chcete připravit projekt, klonovat nebo stáhnout [úložiště sad SDK služby Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) z GitHubu.

Ukázka se nachází ve složce **Samples/Solutions/remote_monitoring_client** .

V textovém editoru otevřete soubor **remote_monitoring. c** ve složce **Samples/Solutions/remote_monitoring_client** .

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Následující postup popisuje, jak pomocí *cmake* vytvořit klientskou aplikaci. Klientská aplikace vzdáleného monitorování je sestavena jako součást procesu sestavení pro sadu SDK.

1. Upravte soubor **remote_monitoring. c** , který chcete nahradit `<connectionstring>` připojovacím řetězcem zařízení, který jste si poznamenali na začátku tohoto průvodce, když jste přidali zařízení do akcelerátoru řešení.

1. Přejděte do kořenového adresáře klonovaného úložiště [sad SDK služby Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) a spuštěním následujících příkazů Sestavte klientskou aplikaci:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Spuštění klientské aplikace a odeslání telemetrie do IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Konzola zobrazuje zprávy jako:

    - Aplikace pošle ukázkovou telemetrii do akcelerátoru řešení.
    - Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
