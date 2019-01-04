---
title: Zřízení zařízení pro Linux pro vzdálené monitorování v jazyce C – Azure | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení pomocí aplikace napsané v jazyce C v Linuxu spuštěnou akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628319"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Připojení zařízení k akcelerátor řešení vzdálené monitorování (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak se připojit skutečné zařízení k akcelerátoru řešení vzdáleného monitorování.

Stejně jako u nejvíce vložené aplikace, která běží na zařízeních s omezením, kód klienta pro zařízení aplikace napsané v C. V tomto kurzu vytvoříte aplikaci na počítači s Ubuntu (Linux).

Pokud chcete simulovat zařízení, přečtěte si téma [vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, je třeba zařízení se systémem Ubuntu verze 15.04 nebo novější. Než budete pokračovat, [nastavení vývojového prostředí Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
