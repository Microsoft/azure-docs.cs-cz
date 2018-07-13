---
title: Připojte Raspberry Pi pomocí jazyka C s Simulovaná telemetrická data sady Azure IoT Suite | Dokumentace Microsoftu
description: Použití sady Microsoft Azure IoT Starter Kit pro Raspberry Pi 3 a Azure IoT Suite. Použití jazyka C pro připojení k řešení vzdáleného monitorování, Raspberry Pi odesílá Simulovaná telemetrická data do cloudu a odpovídat na metody vyvolané z řídicího panelu řešení.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 19b16bff7874a03578b8766668c431553da0d875
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635604"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-c"></a>Připojení vaší Raspberry Pi 3 k řešení vzdáleného monitorování a odesílá Simulovaná telemetrická data pomocí jazyka C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

V tomto kurzu se dozvíte, jak simulovat teploty a vlhkosti data k odeslání do cloudu pomocí Raspberry Pi 3. V tomto kurzu použijete:

- Raspbian operačního systému, programovací jazyk C a Microsoft Azure IoT SDK pro jazyk C k implementaci vzorku zařízení.
- Sadě IoT Suite předkonfigurované řešení vzdáleného monitorování jako cloudové back-endu.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Zřídí řešení vzdáleného monitorování sady služeb Azure ve vašem předplatném Azure. Nasazení odráží skutečný podnikové architektury. Se vyhnout poplatkům za zbytečné využití Azure, odstraňte instanci předkonfigurované řešení na webu azureiotsuite.com po dokončení s ním. Pokud potřebujete předkonfigurovaného řešení, můžete je snadno obnovit. Další informace o snižování spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [předkonfigurovaná řešení pro účely ukázky konfigurace Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Nyní můžete stáhnout a nakonfigurovat vzdáleného monitorování klientské aplikace na Raspberry Pi.

### <a name="clone-the-repositories"></a>Naklonujte úložiště

Pokud jste tak již neučinili, naklonujte spuštěním následujících příkazů v terminálu na vaše číslo pí požadované úložiště:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Aktualizujte připojovací řetězec zařízení

Otevřete zdrojový soubor v ukázkové **nano** editoru pomocí následujícího příkazu:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/remote_monitoring/remote_monitoring.c
```

Vyhledejte následující řádky:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Nahraďte zástupné hodnoty zařízení a informace o službě IoT Hub vytvořili a uložili na začátku tohoto kurzu. Uložte provedené změny (**Ctrl-O**, **Enter**) a ukončete editor (**Ctrl-X**).

## <a name="build-the-sample"></a>Vytvoření vzorku

Nainstalujte požadované balíčky pro zařízení Microsoft Azure IoT SDK pro jazyk C spuštěním následujících příkazů v terminálu na Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Aktualizované ukázkové řešení teď můžete vytvářet v Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
```

Ukázkový program nyní můžete spustit na Raspberry Pi. Zadejte příkaz:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Následující ukázkový výstup je příkladem výstupu, které vidíte na příkazovém řádku na Raspberry Pi:

![Výstup z aplikace Raspberry Pi][img-raspberry-output]

Stisknutím klávesy **Ctrl-C** ukončete program v každém okamžiku.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Další postup

Přejděte [centrum pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot/) Další ukázky a dokumentaci k Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-simulator/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
