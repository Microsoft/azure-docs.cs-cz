---
title: Řešení vzdáleného monitorování přidejte zařízení Edge – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965375"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Přidání zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování

Chcete-li přidat zařízení [IoT Edge](../iot-edge/about-iot-edge.md) do akcelerátoru řešení, proveďte následující dva kroky:

1. Přidejte zařízení Edge na stránku **Průzkumník zařízení** ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování.
1. Nainstalujte runtime IoT Edge na zařízení Edge.

## <a name="add-the-iot-edge-device"></a>Přidání zařízení IoT Edge

Chcete-li přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování, přejděte na stránku **Průzkumníkzařízení** ve webovém uživatelském rozhraní a klepněte na tlačítko **+ Nové zařízení**.

V panelu **Nové zařízení** zvolte **zařízení IoT Edge**. Výchozí hodnoty pro ostatní nastavení můžete ponechat. Pak klikněte na **Použít**:

![Přidání zařízení IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternativní způsoby přidání zařízení IoT Edge

Je také možné zaregistrovat zařízení IoT Edge přímo s instancí Služby IoT Hub v akcelerátoru řešení. Před sledováním některého z těchto návodů na postupy musíte znát název centra IoT hub v akcelerátoru řešení:

- [Registrace nového zařízení Azure IoT Edge z webu Azure Portal](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Registrace nového zařízení Azure IoT Edge pomocí rozhraní příkazového příkazového příkazu Azure](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Registrace nového zařízení Azure IoT Edge z kódu Visual Studia](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Když zaregistrujete zařízení přímo s centrem IoT v akcelerátoru řešení vzdáleného monitorování, je uvedeno na stránce **Průzkumníkzařízení** ve webovém uživatelském rozhraní.

## <a name="install-the-iot-edge-runtime"></a>Instalace runtime IoT Edge

Než budete moct nasadit moduly do zařízení Edge, musíte nainstalovat runtime IoT Edge na skutečné zařízení. Následující návody ukazují, jak nainstalovat runtime na běžné platformy zařízení:

- [Instalace runtime Azure IoT Edge na Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalace runtime Azure IoT Edge na Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Instalace runtime Azure IoT Edge do Windows pro použití s kontejnery windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Instalace runtime Azure IoT Edge do Windows pro použití s linuxovými kontejnery](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Instalace runtime IoT Edge do windows iot core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Další kroky

Teď, když jste připravili zařízení IoT Edge, dalším krokem je nasazení modulů do něj. Viz [Import balíčku IoT Edge do akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-import-edge-package.md)
