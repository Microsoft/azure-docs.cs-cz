---
title: Řešení vzdáleného monitorování přidat hraničního zařízení – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak přidat zařízení IoT Edge k akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 67bfde828287d9892ad404f3d950dbe373503a56
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828204"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Přidat zařízení IoT Edge na akcelerátor řešení vzdálené monitorování

Chcete-li přidat [IoT Edge](../iot-edge/about-iot-edge.md) zařízení akcelerátor řešení, proveďte následující dva kroky:

1. Přidat na hraniční zařízení **zařízení** stránky ve vzdálené monitorování řešení akcelerátoru webového uživatelského rozhraní.
1. Nainstalujte modul runtime IoT Edge na hraniční zařízení.

## <a name="add-the-iot-edge-device"></a>Přidat zařízení IoT Edge

Přidání zařízení IoT Edge na akcelerátor řešení vzdálené monitorování, přejděte **zařízení** stránku ve webovém uživatelském rozhraní a klikněte na tlačítko **+ nové zařízení**.

V **nové zařízení** panelu, vyberte **zařízení IoT Edge**. Můžete ponechat výchozí hodnoty pro ostatní nastavení. Pak klikněte na **Použít**:

![Přidat zařízení IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Alternativní způsoby, jak přidat zařízení IoT Edge

Je také možné zaregistrovat zařízení IoT Edge přímo s instancí služby IoT Hub v akcelerátor řešení. Musíte znát název služby IoT hub v akcelerátor řešení předtím, než budete postupovat podle některého z těchto návody:

- [Zaregistrujte si nové zařízení Azure IoT Edge z portálu Azure portal](../iot-edge/how-to-register-device-portal.md)
- [Pomocí Azure CLI zaregistrujte nové zařízení Azure IoT Edge](../iot-edge/how-to-register-device-cli.md)
- [Zaregistrujte si nové zařízení Azure IoT Edge z Visual Studio Code](../iot-edge/how-to-register-device-vscode.md)

Když registrujete zařízení přímo prostřednictvím služby IoT hub v akcelerátoru řešení vzdáleného monitorování, nejsou na **zařízení** stránku ve webovém uživatelském rozhraní.

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Než bude možné nasadit moduly do vlastního hraničního zařízení, musíte nainstalovat modul runtime IoT Edge na fyzickém zařízení. Průvodce následující postupy ukazují, jak nainstalovat modul runtime na běžné platformách zařízení:

- [Instalace modulu runtime Azure IoT Edge v Linuxu (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalace modulu runtime Azure IoT Edge v Linuxu (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Nainstalovat modul runtime Azure IoT Edge ve Windows pro použití s kontejnery Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Nainstalovat modul runtime Azure IoT Edge ve Windows pro použití s kontejnery Linuxu](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Nainstalovat modul runtime IoT Edge ve Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Další postup

Teď připravíte zařízení IoT Edge, dalším krokem je můžete nasadit moduly do něj. Zobrazit [importovat balíček IoT Edge do akcelerátor řešení vzdálené monitorování](iot-accelerators-remote-monitoring-import-edge-package.md)
