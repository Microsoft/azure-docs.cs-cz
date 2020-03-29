---
title: Zřízení zařízení Linux u vzdáleného monitorování v C – Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak připojit zařízení k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v C běžící na Linuxu.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454487"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Připojení zařízení k akcelerátoru řešení vzdáleného monitorování (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se můžete připojit skutečné zařízení k akcelerátoru řešení vzdáleného monitorování.

Stejně jako u většiny vložených aplikací, které běží na omezených zařízeních, klientský kód pro aplikaci zařízení je napsán v C. V tomto kurzu můžete vytvořit aplikaci na počítači se systémem Ubuntu (Linux).

Pokud dáváte přednost simulaci zařízení, přečtěte si informace [o vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit kroky v tomto návodu, potřebujete zařízení se systémem Ubuntu verze 15.04 nebo novějším. Než budete pokračovat, [nastavte si vývojové prostředí Linuxu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
