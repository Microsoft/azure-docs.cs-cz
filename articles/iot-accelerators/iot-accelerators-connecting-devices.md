---
title: Zřízení zařízení s Windows ke vzdálenému monitorování v C – Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak připojit zařízení k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v C spuštěné v systému Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450210"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Připojení zařízení k akcelerátoru řešení vzdáleného monitorování (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se můžete připojit skutečné zařízení k akcelerátoru řešení vzdáleného monitorování.

Stejně jako u většiny vložených aplikací, které běží na omezených zařízeních, klientský kód pro aplikaci zařízení je napsán v C. V tomto kurzu vytvoříte klientskou aplikaci zařízení v počítači se systémem Windows.

Pokud dáváte přednost simulaci zařízení, přečtěte si informace [o vytvoření a testování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, postupujte podle pokynů v [nastavení vývojového prostředí systému Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) a přidejte požadované vývojové nástroje a knihovny do počítače se systémem Windows.

## <a name="view-the-code"></a>Zobrazit kód

[Ukázkový kód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) použitý v této příručce je k dispozici v úložišti GitHub Azure IoT C SDKs.

### <a name="download-the-source-code-and-prepare-the-project"></a>Stáhněte si zdrojový kód a připravte projekt

Chcete-li připravit projekt, [klonování úložiště Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) z GitHubu.

Vzorek je umístěn ve složce **vzorky/řešení/remote_monitoring_client.**

Otevřete soubor **remote_monitoring.c** ve složce **samples/solutions/remote_monitoring_client** v textovém editoru.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Upravte soubor **remote_monitoring.c,** který chcete nahradit `<connectionstring>` připojovacím řetězcem zařízení, který jste si poznamenali na začátku tohoto návodu, když jste přidali zařízení do akcelerátoru řešení.

1. Postupujte podle kroků v [části Sestavení sady C SDK v systému Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) k vytvoření sady SDK a klientské aplikace vzdáleného monitorování.

1. Na příkazovém řádku, který jste použili k vytvoření řešení, spusťte:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Konzole zobrazuje zprávy jako:

    - Aplikace odešle ukázkovou telemetrii do akcelerátoru řešení.
    - Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
