---
title: Připojte Raspberry Pi Azure IoT Suite pro podporu aktualizace firmwaru pomocí Node.js | Dokumentace Microsoftu
description: Použití sady Microsoft Azure IoT Starter Kit pro Raspberry Pi 3 a Azure IoT Suite. Použití Node.js k připojení k řešení vzdáleného monitorování, Raspberry Pi odesílání telemetrických dat ze senzorů do cloudu a provede aktualizaci firmwaru vzdálené.
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
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309587"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Připojení vaší Raspberry Pi 3 k řešení vzdáleného monitorování a povolit tak aktualizace firmwaru vzdálené pomocí Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

V tomto kurzu se dozvíte, jak používat Microsoft Azure IoT Starter Kit pro Raspberry Pi 3:

* Vyvíjejte čtečku teploty a vlhkosti, který může komunikovat s cloudem.
* Povolte a proveďte vzdálené firmware aktualizace pro aktualizaci klientské aplikace na Raspberry Pi.

V tomto kurzu použijete:

- Raspbian operačního systému, programovacího jazyka Node.js a Microsoft Azure IoT SDK pro Node.js k implementaci vzorku zařízení.
- Sadě IoT Suite předkonfigurované řešení vzdáleného monitorování jako cloudové back-endu.

## <a name="overview"></a>Přehled

V tomto kurzu provedete následující kroky:

- Nasaďte instanci tohoto předkonfigurovaného řešení vzdáleného monitorování ke svému předplatnému Azure. Tento krok se automaticky nasadí a nakonfiguruje několik služeb Azure.
- Nastavení zařízení a senzorů ke komunikaci s počítačem a řešení vzdáleného monitorování.
- Aktualizace vzorového kódu zařízení pro připojení k řešení vzdáleného monitorování a odesílání telemetrických dat, který se zobrazí na řídicím panelu řešení.
- Použití ukázkového kódu zařízení k aktualizaci klienta aplikace.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Zřídí řešení vzdáleného monitorování sady služeb Azure ve vašem předplatném Azure. Nasazení odráží skutečný podnikové architektury. Se vyhnout poplatkům za zbytečné využití Azure, odstraňte instanci předkonfigurované řešení na webu azureiotsuite.com po dokončení s ním. Pokud potřebujete předkonfigurovaného řešení, můžete je snadno obnovit. Další informace o snižování spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [předkonfigurovaná řešení pro účely ukázky konfigurace Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Nyní můžete stáhnout a nakonfigurovat vzdáleného monitorování klientské aplikace na Raspberry Pi.

### <a name="install-nodejs"></a>Instalovat Node.js

Pokud jste tak ještě neučinili, nainstalujte Node.js v Raspberry Pi. IoT SDK pro Node.js vyžaduje verzi 0.11.5 Node.js nebo novější. Následující kroky ukazují, jak nainstalovat Node.js v6.10.2 v Raspberry Pi:

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

Pokud jste tak ještě neučinili, naklonujte spuštěním následujících příkazů na vaše číslo pí požadované úložiště:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Aktualizujte připojovací řetězec zařízení

Otevřete ukázkový konfigurační soubor v **nano** editoru pomocí následujícího příkazu:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Nahraďte zástupné hodnoty id zařízení a informace o službě IoT Hub vytvořili a uložili na začátku tohoto kurzu.

Jakmile budete hotovi, obsah souboru deviceinfo by měl vypadat jako v následujícím příkladu:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Uložte provedené změny (**Ctrl-O**, **Enter**) a ukončete editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Spuštění ukázky

Spuštěním následujících příkazů nainstalujte požadované balíčky pro ukázku:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Ukázkový program nyní můžete spustit na Raspberry Pi. Zadejte příkaz:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

Následující ukázkový výstup je příkladem výstupu, které vidíte na příkazovém řádku na Raspberry Pi:

![Výstup z aplikace Raspberry Pi][img-raspberry-output]

Stisknutím klávesy **Ctrl-C** ukončete program v každém okamžiku.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Na řídicím panelu řešení, klikněte na tlačítko **zařízení** přejdete **zařízení** stránky. Vyberte v Raspberry Pi **seznam zařízení**. Klikněte na tlačítko **metody**:

    ![Seznam zařízení na řídicím panelu][img-list-devices]

1. Na **vyvolání metody** zvolte **InitiateFirmwareUpdate** v **metoda** rozevíracího seznamu.

1. V **FWPackageURI** zadejte **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Tento soubor obsahuje implementaci firmwaru verze 2.0.

1. Zvolte **InvokeMethod**. Aplikace na Raspberry Pi potvrzení odešle zpět na řídicí panel řešení. Stáhněte si novou verzi firmwaru pak spustí proces aktualizace firmwaru:

    ![Zobrazit historii – metoda][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Sledujte proces aktualizace firmwaru

Můžete sledovat firmware aktualizovat proces běží na zařízení a podívejte se na řídicím panelu řešení ohlášených vlastností:

1. Můžete zobrazit průběh v procesu aktualizace na Raspberry Pi:

    ![Zobrazit průběh aktualizace][img-update-progress]

    > [!NOTE]
    > Vzdálené monitorování aplikací restartuje tiše po dokončení aktualizace. Použijte příkaz `ps -ef` ověření je spuštěná. Pokud chcete proces ukončit, použijte `kill` příkazu s id procesu.

1. Stav aktualizace firmwaru, můžete zobrazit, jak je hlásí zařízení na portálu řešení. Následující snímek obrazovky ukazuje stav a doba trvání každou z fází procesu aktualizace a nové verze firmwaru:

    ![Zobrazit stav úlohy][img-job-status]

    Když přejdete zpět na řídicí panel, můžete ověřit, že zařízení stále odesílá telemetrická data po aktualizaci firmwaru.

> [!WARNING]
> Pokud jste toto řešení vzdáleného monitorování spuštěné v účtu Azure, se účtují po dobu, kterou poběží. Další informace o snižování spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [předkonfigurovaná řešení pro účely ukázky konfigurace Azure IoT Suite][lnk-demo-config]. Odstraňte předkonfigurované řešení z vašeho účtu Azure, po dokončení jeho použití.

## <a name="next-steps"></a>Další postup

Přejděte [centrum pro vývojáře Azure IoT](https://azure.microsoft.com/develop/iot/) Další ukázky a dokumentaci k Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
