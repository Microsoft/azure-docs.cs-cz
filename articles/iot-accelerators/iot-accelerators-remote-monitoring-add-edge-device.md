---
title: Řešení vzdáleného monitorování přidání hraničního zařízení – Azure | Microsoft Docs
description: Tento článek popisuje, jak přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72965375"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Přidání zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování

Pokud chcete přidat zařízení [IoT Edge](../iot-edge/about-iot-edge.md) do akcelerátoru řešení, proveďte následující dva kroky:

1. Přidejte hraniční zařízení na stránce **Device Explorer** ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování.
1. Nainstalujte modul runtime IoT Edge do hraničního zařízení.

## <a name="add-the-iot-edge-device"></a>Přidání zařízení IoT Edge

Pokud chcete přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování, přejděte na stránku **Device Explorer** ve webovém uživatelském rozhraní a klikněte na **+ nové zařízení**.

Na panelu **nové zařízení** vyberte **IoT Edge zařízení**. Můžete ponechat výchozí hodnoty pro ostatní nastavení. Pak klikněte na **Použít**:

![Přidat IoT Edge zařízení](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternativní způsoby, jak přidat zařízení IoT Edge

Je také možné zaregistrovat IoT Edge zařízení přímo s instancí IoT Hub v akcelerátoru řešení. Než začnete s některým z těchto pokynů, musíte znát název služby IoT Hub v akcelerátoru řešení:

- [Registrovat nové zařízení Azure IoT Edge z Azure Portal](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Registrace nového zařízení Azure IoT Edge pomocí Azure CLI](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Registrovat nové zařízení Azure IoT Edge z Visual Studio Code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Když zaregistrujete zařízení přímo do centra IoT v akcelerátoru řešení vzdáleného monitorování, zobrazí se na stránce **Device Explorer** ve webovém uživatelském rozhraní.

## <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Předtím, než budete moci nasadit moduly do hraničního zařízení, je nutné na reálné zařízení nainstalovat modul runtime IoT Edge. Následující příručky s postupy vám ukážou, jak nainstalovat modul runtime na běžných platformách zařízení:

- [Instalace modulu runtime Azure IoT Edge v systému Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalace modulu runtime Azure IoT Edge v systému Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Instalace modulu runtime Azure IoT Edge ve Windows pro použití s kontejnery Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Instalace modulu runtime Azure IoT Edge ve Windows pro použití s kontejnery pro Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Instalace modulu runtime IoT Edge v systému Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Další kroky

Teď, když jste připravili zařízení IoT Edge, je dalším krokem nasazení modulů do tohoto zařízení. Viz [Import balíčku IoT Edge do akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-import-edge-package.md) .
