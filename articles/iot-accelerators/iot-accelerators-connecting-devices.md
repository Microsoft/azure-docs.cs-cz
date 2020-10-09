---
title: Zřizování zařízení s Windows pro vzdálené monitorování v C-Azure | Microsoft Docs
description: Popisuje, jak připojit zařízení k akcelerátoru řešení vzdáleného monitorování pomocí aplikace napsané v jazyce C běžícího v systému Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "61450210"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Připojení zařízení k akcelerátoru řešení vzdáleného monitorování (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojit reálné zařízení k akcelerátoru řešení vzdáleného monitorování.

Stejně jako u většiny vložených aplikací, které běží na omezených zařízeních, kód klienta pro aplikaci zařízení je napsán v jazyce C. V tomto kurzu vytvoříte klientskou aplikaci zařízení na počítači s Windows.

Pokud dáváte přednost simulaci zařízení, přečtěte si téma [Vytvoření a otestování nového simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci návodem postupujte podle kroků v části [Nastavení vývojového prostředí Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) pro přidání požadovaných vývojových nástrojů a knihoven do počítače s Windows.

## <a name="view-the-code"></a>Zobrazit kód

[Vzorový kód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) použitý v tomto průvodci je k dispozici v úložišti GitHub pro Azure IoT C SDK.

### <a name="download-the-source-code-and-prepare-the-project"></a>Stažení zdrojového kódu a příprava projektu

Chcete-li připravit projekt, [naklonujte úložiště sad SDK služby Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) z GitHubu.

Ukázka se nachází ve složce **Samples/Solutions/remote_monitoring_client** .

V textovém editoru otevřete soubor **remote_monitoring. c** ve složce **Samples/Solutions/remote_monitoring_client** .

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Upravte soubor **remote_monitoring. c** , který chcete nahradit `<connectionstring>` připojovacím řetězcem zařízení, který jste si poznamenali na začátku tohoto průvodce, když jste přidali zařízení do akcelerátoru řešení.

1. Postupujte podle kroků v [části Vytvoření sady C SDK v systému Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) k sestavení sady SDK a klientské aplikace vzdáleného monitorování.

1. Na příkazovém řádku, který jste použili k sestavení řešení, spusťte příkaz:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Konzola zobrazuje zprávy jako:

    - Aplikace pošle ukázkovou telemetrii do akcelerátoru řešení.
    - Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
