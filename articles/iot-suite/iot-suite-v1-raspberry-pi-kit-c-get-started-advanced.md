---
title: Připojte Raspberry Pi pomocí jazyka C pro podporu aktualizace firmwaru Azure IoT Suite | Dokumentace Microsoftu
description: Použití sady Microsoft Azure IoT Starter Kit pro Raspberry Pi 3 a Azure IoT Suite. Použití jazyka C pro připojení k řešení vzdáleného monitorování, Raspberry Pi odesílání telemetrických dat ze senzorů do cloudu a provede aktualizaci firmwaru vzdálené.
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
ms.openlocfilehash: 8160752b0116c3ef3e6b6ab7920bb35e471f180b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687691"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>Připojení vaší Raspberry Pi 3 k řešení vzdáleného monitorování a povolit tak aktualizace firmwaru vzdálené pomocí jazyka C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

V tomto kurzu se dozvíte, jak používat Microsoft Azure IoT Starter Kit pro Raspberry Pi 3:

* Vyvíjejte čtečku teploty a vlhkosti, který může komunikovat s cloudem.
* Povolte a proveďte vzdálené firmware aktualizace pro aktualizaci klientské aplikace na Raspberry Pi.

V tomto kurzu použijete:

* Raspbian operačního systému, programovací jazyk C a Microsoft Azure IoT SDK pro jazyk C k implementaci vzorku zařízení.
* Sadě IoT Suite předkonfigurované řešení vzdáleného monitorování jako cloudové back-endu.

## <a name="overview"></a>Přehled

V tomto kurzu provedete následující kroky:

* Nasaďte instanci tohoto předkonfigurovaného řešení vzdáleného monitorování ke svému předplatnému Azure. Tento krok se automaticky nasadí a nakonfiguruje několik služeb Azure.
* Nastavení zařízení a senzorů ke komunikaci s počítačem a řešení vzdáleného monitorování.
* Aktualizace vzorového kódu zařízení pro připojení k řešení vzdáleného monitorování a odesílání telemetrických dat, který se zobrazí na řídicím panelu řešení.
* Použití ukázkového kódu zařízení k aktualizaci klienta aplikace.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Zřídí řešení vzdáleného monitorování sady služeb Azure ve vašem předplatném Azure. Nasazení odráží skutečný podnikové architektury. Se vyhnout poplatkům za zbytečné využití Azure, odstraňte instanci předkonfigurované řešení na webu azureiotsuite.com po dokončení s ním. Pokud potřebujete předkonfigurovaného řešení, můžete je snadno obnovit. Další informace o snižování spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [předkonfigurovaná řešení pro účely ukázky konfigurace Azure IoT Suite][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Nyní můžete stáhnout a nakonfigurovat vzdáleného monitorování klientské aplikace na Raspberry Pi.

### <a name="clone-the-repositories"></a>Naklonujte úložiště

Pokud jste tak ještě neučinili, naklonujte spuštěním následujících příkazů na vaše číslo pí požadované úložiště:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Aktualizujte připojovací řetězec zařízení

Otevřete ukázkový konfigurační soubor v **nano** editoru pomocí následujícího příkazu:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Nahraďte zástupné hodnoty ID a IoT Hub informací o zařízení jste vytvořili a uložili na začátku tohoto kurzu.

Jakmile budete hotovi, obsah souboru deviceinfo by měl vypadat jako v následujícím příkladu:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Uložte provedené změny (**Ctrl-O**, **Enter**) a ukončete editor (**Ctrl-X**).

## <a name="build-the-sample"></a>Vytvoření vzorku

Pokud jste tak již neučinili, nainstalujte požadované balíčky pro zařízení Microsoft Azure IoT SDK pro jazyk C spuštěním následujících příkazů v terminálu na Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Ukázkové řešení teď můžete vytvářet v Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Ukázkový program nyní můžete spustit na Raspberry Pi. Zadejte příkaz:

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

Následující ukázkový výstup je příkladem výstupu, které vidíte na příkazovém řádku na Raspberry Pi:

![Výstup z aplikace Raspberry Pi][img-raspberry-output]

Stisknutím klávesy **Ctrl-C** ukončete program v každém okamžiku.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Na řídicím panelu řešení, klikněte na tlačítko **zařízení** přejdete **zařízení** stránky. Vyberte v Raspberry Pi **seznam zařízení**. Klikněte na tlačítko **metody**:

    ![Seznam zařízení na řídicím panelu][img-list-devices]

1. Na **vyvolání metody** zvolte **InitiateFirmwareUpdate** v **metoda** rozevíracího seznamu.

1. V **FWPackageURI** zadejte **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Tento soubor archivu obsahuje implementaci firmwaru verze 2.0.

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


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md