---
title: Připojení k Azure IoT Suite pomocí Node.js skutečné snímači malin platformy | Microsoft Docs
description: Pomocí Startovní sady Microsoft Azure IoT Malinová pí 3 a sady Azure IoT Suite. Použití Node.js pro vaše platformy malin připojení k řešení vzdáleného monitorování, odesílat telemetrická data ze senzorů do cloudu a reagovat na metody vyvolané z řídicího panelu řešení.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bb4f62b1a3de68ce8796b60fe76cd97b9ab9ade
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2017
ms.locfileid: "24011963"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>Připojení k řešení vzdáleného monitorování vaší malin pí 3 a odesílat telemetrická data z reálného senzor pomocí Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

V tomto kurzu se dozvíte, jak používat Microsoft Azure IoT Starter Kit pro malin pí 3 k vývoji čtečku teploty a vlhkosti, který může komunikovat s cloudem. Tento kurz používá:

- Raspbian operačního systému, programovací jazyk Node.js a Microsoft Azure IoT SDK pro Node.js implementovat ukázka zařízení.
- Sadě IoT Suite předkonfigurované řešení vzdáleného monitorování jako cloudové back-end.

## <a name="overview"></a>Přehled

V tomto kurzu je provést následující kroky:

- Nasaďte instanci předkonfigurovaného řešení vzdáleného monitorování k předplatnému Azure. Tento krok automaticky nasadí a nakonfiguruje více služeb Azure.
- Nastavte zařízením a senzory ke komunikaci s vaším počítačem a řešení vzdáleného monitorování.
- Aktualizujte zařízení ukázkový kód pro připojení k řešení vzdáleného monitorování a odesílat telemetrická data, která můžete zobrazit na řídicí panel řešení.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Řešení vzdáleného monitorování zřídí sadu služeb Azure ve vašem předplatném Azure. Nasazení odráží architektura skutečné enterprise. Aby se zabránilo zbytečným využití platformy Azure poplatky, odstraňte instanci předkonfigurované řešení na azureiotsuite.com po dokončení s ním. Pokud budete znovu potřebovat předkonfigurované řešení, můžete ho snadno obnovit. Další informace o snížení spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [konfigurace Azure IoT Suite předkonfigurovaných řešení pro účely ukázky][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Stáhnout a nakonfigurovat vzorku

Teď můžete stáhnout a nakonfigurovat vzdálené monitorování klientskou aplikaci na vaše malin platformy.

### <a name="install-nodejs"></a>Instalovat Node.js

Nainstalujte Node.js na vaše Malinová pí. Sada IoT SDK pro Node.js vyžaduje verzi 0.11.5 Node.js nebo novější. Následující kroky vám ukážou, jak nainstalovat Node.js v6.10.2 na vaše malin platformy:

1. Použijte následující příkaz k aktualizaci vašeho malin platformy:

    ```sh
    sudo apt-get update
    ```

1. Stažení Node.js binární soubory pro vaše platformy malin použijte následující příkaz:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Použijte následující příkaz pro instalaci binárních souborů:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Pomocí následujícího příkazu ověřte, že jste úspěšně nainstalovali Node.js v6.10.2:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Klonování úložiště

Pokud jste tak již neučinili, klonování vyžaduje úložiště spuštěním následujících příkazů na vašeho platformy:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`
```

### <a name="update-the-device-connection-string"></a>Aktualizovat připojovací řetězec zařízení

Otevření zdrojového souboru ukázka v **nano** editor pomocí následujícího příkazu:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Vyhledejte řádek:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Zástupné hodnoty nahraďte zařízení a informace služby IoT Hub vytvořili a uložili na začátku tohoto kurzu. Uložte změny (**Ctrl-O**, **Enter**) a ukončete editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Spustit ukázku

Spusťte následující příkazy pro instalaci požadovaných balíčků pro ukázku:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic
npm install
```

Teď můžete spustit ukázkový program na malin pí. Zadejte příkaz:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Následující ukázkový výstup je příklad výstupu, které vidíte na příkazovém řádku na malin platformy:

![Výstup z aplikace Malinová platformy][img-raspberry-output]

Stiskněte klávesu **Ctrl-C** ukončete program kdykoli.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Další kroky

Přejděte [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) Další ukázky a dokumentace na Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
