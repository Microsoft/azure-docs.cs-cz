---
title: Zřízení zařízení Windows pro vzdálené monitorování v jazyce C – Azure | Dokumentace Microsoftu
description: Popisuje, jak připojit zařízení k akcelerátoru řešení vzdáleného monitorování, který se pomocí aplikace napsané v jazyce C a systémem Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 55c8ff799ba3ff7fe9691d46dc90a00d5182d390
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829406"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Připojení zařízení k akcelerátor řešení vzdálené monitorování (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

V tomto kurzu se dozvíte, jak připojení fyzických zařízení k akcelerátoru řešení vzdáleného monitorování.

Stejně jako u nejvíce vložené aplikace, která běží na zařízeních s omezením, kód klienta pro zařízení aplikace napsané v C. V tomto kurzu vytvoříte aplikaci klienta zařízení na počítači se systémem Windows.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, postupujte podle kroků v [nastavení vývojového prostředí Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) přidat požadované vývojářské nástroje a knihovny do počítače Windows.

## <a name="view-the-code"></a>Zobrazení kódu

[Ukázkový kód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) použitý v tomto průvodci je k dispozici v úložišti Azure IoT C SDK na Githubu.

### <a name="download-the-source-code-and-prepare-the-project"></a>Stáhněte si zdrojový kód a příprava projektu

Příprava projektu, klonovat nebo stáhnout [úložiště sad SDK Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) z Githubu.

Tato ukázka se nachází v **ukázky a řešení/remote_monitoring_client** složky.

Otevřít **remote_monitoring.c** soubor **ukázky a řešení/remote_monitoring_client** složky v textovém editoru.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Upravit **remote_monitoring.c** souboru nahraďte `<connectionstring>` připojovacím řetězcem zařízení, které jste si poznamenali na začátku příručky s postupy po přidání zařízení do akcelerátoru řešení.

1. Postupujte podle kroků v [sestavení sady SDK jazyka C ve Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) sestavení sady SDK a vzdálené aplikace monitorování klienta.

1. Na příkazovém řádku, který jste použili k vytvoření řešení spusťte:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Této konzole se zobrazují zprávy jako:

    - Aplikace odesílá telemetrii ukázka akcelerátor řešení.
    - Reaguje na metody vyvolané z řídicího panelu řešení.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
