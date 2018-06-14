---
title: Připojení k Azure IoT Suite pomocí Node.js pro podporu aktualizace firmwaru malin platformy | Microsoft Docs
description: Pomocí Startovní sady Microsoft Azure IoT Malinová pí 3 a sady Azure IoT Suite. Použití Node.js pro vaše platformy malin připojení k řešení vzdáleného monitorování, odesílat telemetrická data ze snímače do cloudu a provést aktualizaci firmwaru vzdálené.
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
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012110"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Připojení k řešení vzdáleného monitorování vaší malin pí 3 a povolit vzdálenou firmware aktualizace pomocí Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Tento kurz ukazuje, jak pomocí Startovní sady Microsoft Azure IoT pro malin pí 3 pro:

* Vývoj čtečku teploty a vlhkosti, který může komunikovat s cloudem.
* Povolit a provádět vzdálenou firmware aktualizace nebo aktualizace klientská aplikace na malin pí.

Tento kurz používá:

- Raspbian operačního systému, programovací jazyk Node.js a Microsoft Azure IoT SDK pro Node.js implementovat ukázka zařízení.
- Sadě IoT Suite předkonfigurované řešení vzdáleného monitorování jako cloudové back-end.

## <a name="overview"></a>Přehled

V tomto kurzu je provést následující kroky:

- Nasaďte instanci předkonfigurovaného řešení vzdáleného monitorování k předplatnému Azure. Tento krok automaticky nasadí a nakonfiguruje více služeb Azure.
- Nastavte zařízením a senzory ke komunikaci s vaším počítačem a řešení vzdáleného monitorování.
- Aktualizujte zařízení ukázkový kód pro připojení k řešení vzdáleného monitorování a odesílat telemetrická data, která můžete zobrazit na řídicí panel řešení.
- Ukázkový kód zařízení použijte k aktualizaci klientské aplikace.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Řešení vzdáleného monitorování zřídí sadu služeb Azure ve vašem předplatném Azure. Nasazení odráží architektura skutečné enterprise. Aby se zabránilo zbytečným využití platformy Azure poplatky, odstraňte instanci předkonfigurované řešení na azureiotsuite.com po dokončení s ním. Pokud budete znovu potřebovat předkonfigurované řešení, můžete ho snadno obnovit. Další informace o snížení spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [konfigurace Azure IoT Suite předkonfigurovaných řešení pro účely ukázky][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Stáhnout a nakonfigurovat vzorku

Teď můžete stáhnout a nakonfigurovat vzdálené monitorování klientskou aplikaci na vaše malin platformy.

### <a name="install-nodejs"></a>Instalovat Node.js

Pokud jste tak ještě neučinili, nainstalujte na vaše platformy malin Node.js. Sada IoT SDK pro Node.js vyžaduje verzi 0.11.5 Node.js nebo novější. Následující kroky vám ukážou, jak nainstalovat Node.js v6.10.2 na vaše malin platformy:

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

Pokud jste tak ještě neučinili, klonování vyžaduje úložiště spuštěním následujících příkazů na vašeho platformy:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Aktualizovat připojovací řetězec zařízení

Otevřete konfigurační soubor ukázka v **nano** editor pomocí následujícího příkazu:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Zástupné hodnoty nahraďte id zařízení a informace služby IoT Hub vytvořili a uložili na začátku tohoto kurzu.

Až skončíte, obsah souboru deviceinfo by měl vypadat jako v následujícím příkladu:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Uložte změny (**Ctrl-O**, **Enter**) a ukončete editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Spustit ukázku

Spusťte následující příkazy pro instalaci požadovaných balíčků pro ukázku:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Teď můžete spustit ukázkový program na malin pí. Zadejte příkaz:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

Následující ukázkový výstup je příklad výstupu, které vidíte na příkazovém řádku na malin platformy:

![Výstup z aplikace Malinová platformy][img-raspberry-output]

Stiskněte klávesu **Ctrl-C** ukončete program kdykoli.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Na řídicím panelu řešení, klikněte na tlačítko **zařízení** přejděte **zařízení** stránky. Vyberte vaše Malinová platformy v **seznam zařízení**. Zvolte **metody**:

    ![Seznam zařízení na řídicím panelu][img-list-devices]

1. Na **vyvolání metody** vyberte **InitiateFirmwareUpdate** v **metoda** rozevíracího seznamu.

1. V **FWPackageURI** zadejte **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Tento soubor obsahuje implementace 2.0 verzi firmwaru.

1. Zvolte **InvokeMethod**. Aplikace na platformy malin odešle na potvrzení zpět na řídicí panel řešení. Pak spustí proces aktualizace firmwaru stažením novou verzi firmwaru:

    ![Zobrazit historii – metoda][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Sledovat firmware proces aktualizovat.

Můžete sledovat firmware proces aktualizovat. při jeho spuštění v zařízení a zobrazením hlášen vlastnostech na řídicím panelu řešení:

1. Postup v procesu aktualizace lze zobrazit na malin platformy:

    ![Ukázat průběh aktualizace][img-update-progress]

    > [!NOTE]
    > Vzdálené monitorování aplikace se restartuje bez upozornění po dokončení aktualizace. Použijte příkaz `ps -ef` ověření je spuštěná. Pokud chcete ukončit proces, pomocí `kill` s id procesu.

1. Podle zařízení, na portálu řešení můžete zobrazit stav aktualizace firmwaru. Následující snímek obrazovky ukazuje stav a doba trvání u každé fáze procesu aktualizace a nová verze firmwaru:

    ![Zobrazit stav úlohy][img-job-status]

    Pokud přejdete zpět na řídicí panel, můžete ověřit, že zařízení je stále odesílat telemetrii následující aktualizace firmwaru.

> [!WARNING]
> Pokud necháte řešení vzdáleného monitorování spuštěné v účtu Azure, se vám účtuje v době spuštění. Další informace o snížení spotřeby průběhu řešení vzdáleného monitorování najdete v tématu [konfigurace Azure IoT Suite předkonfigurovaných řešení pro účely ukázky][lnk-demo-config]. Odstraňte předkonfigurované řešení z účtu Azure, když přestanete používat.

## <a name="next-steps"></a>Další kroky

Přejděte [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) Další ukázky a dokumentace na Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
