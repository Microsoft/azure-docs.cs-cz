---
title: Připojte Raspberry Pi Azure IoT Suite skutečných senzorů pomocí Node.js | Dokumentace Microsoftu
description: Použití sady Microsoft Azure IoT Starter Kit pro Raspberry Pi 3 a Azure IoT Suite. Použití Node.js k připojení k řešení vzdáleného monitorování, Raspberry Pi odesílání telemetrických dat ze senzorů do cloudu a odpovídat na metody vyvolané z řídicího panelu řešení.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299285"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>Připojení vaší Raspberry Pi 3 k řešení vzdáleného monitorování a odesílat telemetrická data z reálných senzorů pomocí Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

V tomto kurzu se dozvíte, jak vyvíjet čtečku teploty a vlhkosti, který může komunikovat s cloudem pomocí Microsoft Azure IoT Starter Kit pro Raspberry Pi 3. V tomto kurzu použijete:

- Raspbian operačního systému, programovacího jazyka Node.js a Microsoft Azure IoT SDK pro Node.js k implementaci vzorku zařízení.
- Sadě IoT Suite předkonfigurované řešení vzdáleného monitorování jako cloudové back-endu.

## <a name="overview"></a>Přehled

V tomto kurzu provedete následující kroky:

- Nasaďte instanci tohoto předkonfigurovaného řešení vzdáleného monitorování ke svému předplatnému Azure. Tento krok se automaticky nasadí a nakonfiguruje několik služeb Azure.
- Nastavení zařízení a senzorů ke komunikaci s počítačem a řešení vzdáleného monitorování.
- Aktualizace vzorového kódu zařízení pro připojení k řešení vzdáleného monitorování a odesílání telemetrických dat, který se zobrazí na řídicím panelu řešení.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Zřídí řešení vzdáleného monitorování sady služeb Azure ve vašem předplatném Azure. Nasazení odráží skutečný podnikové architektury. Se vyhnout poplatkům za zbytečné využití Azure, odstraňte instanci předkonfigurované řešení na webu azureiotsuite.com po dokončení s ním. Pokud potřebujete předkonfigurovaného řešení, můžete je snadno obnovit. Další informace o snižování spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [předkonfigurovaná řešení pro účely ukázky konfigurace Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Nyní můžete stáhnout a nakonfigurovat vzdáleného monitorování klientské aplikace na Raspberry Pi.

### <a name="install-nodejs"></a>Instalovat Node.js

Nainstalujte Node.js v Raspberry Pi. IoT SDK pro Node.js vyžaduje verzi 0.11.5 Node.js nebo novější. Následující kroky ukazují, jak nainstalovat Node.js v6.10.2 v Raspberry Pi:

1. Chcete-li aktualizovat Raspberry Pi, použijte následující příkaz:

    ```sh
    sudo apt-get update
    ```

1. Chcete-li stáhnout Node.js binární soubory na Raspberry Pi, použijte následující příkaz:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Použijte následující příkaz k instalaci binárních souborů:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Chcete-li ověřit, že máte nainstalované Node.js v6.10.2 úspěšně použijte následující příkaz:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Naklonujte úložiště

Pokud jste tak již neučinili, naklonujte spuštěním následujících příkazů na vaše číslo pí požadované úložiště:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`
```

### <a name="update-the-device-connection-string"></a>Aktualizujte připojovací řetězec zařízení

Otevřete zdrojový soubor v ukázkové **nano** editoru pomocí následujícího příkazu:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Vyhledejte řádek:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Nahraďte zástupné hodnoty zařízení a informace o službě IoT Hub vytvořili a uložili na začátku tohoto kurzu. Uložte provedené změny (**Ctrl-O**, **Enter**) a ukončete editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Spuštění ukázky

Spuštěním následujících příkazů nainstalujte požadované balíčky pro ukázku:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic
npm install
```

Ukázkový program nyní můžete spustit na Raspberry Pi. Zadejte příkaz:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Následující ukázkový výstup je příkladem výstupu, které vidíte na příkazovém řádku na Raspberry Pi:

![Výstup z aplikace Raspberry Pi][img-raspberry-output]

Stisknutím klávesy **Ctrl-C** ukončete program v každém okamžiku.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Další postup

Přejděte [centrum pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot/) Další ukázky a dokumentaci k Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
