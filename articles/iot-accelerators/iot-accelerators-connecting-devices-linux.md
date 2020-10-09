---
title: Zřizování zařízení se systémem Linux pro vzdálené monitorování v jazyce C – Azure | Microsoft Docs
description: Popisuje, jak připojit zařízení k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v jazyce C běžícího na systému Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454487"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Připojení zařízení k akcelerátoru řešení vzdáleného monitorování (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojit reálné zařízení k akcelerátoru řešení vzdáleného monitorování.

Stejně jako u většiny vložených aplikací, které běží na omezených zařízeních, kód klienta pro aplikaci zařízení je napsán v jazyce C. V tomto kurzu sestavíte aplikaci na počítači se systémem Ubuntu (Linux).

Pokud dáváte přednost simulaci zařízení, přečtěte si téma [Vytvoření a otestování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete zařízení se systémem Ubuntu verze 15,04 nebo novější. Než budete pokračovat, [nastavte své vývojové prostředí pro Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
