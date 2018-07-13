---
title: Jak nainstalovat Azure IoT Edge v Linuxu | Dokumentace Microsoftu
description: Instalační pokyny pro Azure IoT Edge v Linuxu na ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5b5212d5e1663fee01ff87642432818071d4f4dd
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988530"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalace modulu runtime Azure IoT Edge v Linuxu (ARM32v7/armhf)

Modul runtime Azure IoT Edge je nasadit na všechna zařízení IoT Edge. Skládá se ze tří částí. **Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Proces démon spustí při každé spuštění a bootstraps zařízení spuštěním agenta IoT Edge. **Agenta IoT Edge** usnadňuje nasazení a monitorování modulů na hraniční zařízení, včetně hraničních zařízeních IoT hub. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Tento článek uvádí postup instalace modulu runtime Azure IoT Edge v Linuxu ARM32v7/armhf hraničním zařízení (například Raspberry Pi).

>[!NOTE]
>Balíčky v úložiště softwaru Linux jsou souladu s licenčními podmínkami v jednotlivých balíčků (/ usr/sdílet/doc/*název balíčku*). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

Azure IoT Edge se může spolehnout [OCI kompatibilní] [ lnk-oci] kontejner modulu runtime. Pro produkční scénáře, důrazně doporučujeme vám použít [na základě Moby] [ lnk-moby] níže uvedený modul. Je modul pouze container oficiálně podporované službou Azure IoT Edge. Image kontejnerů dockeru CE/EE jsou kompatibilní s modulem runtime na základě Moby.

Následujících příkazů nainstalujte základě Moby modul a rozhraní příkazového řádku (CLI). Rozhraní příkazového řádku je užitečné pro vývoj, ale volitelný pro nasazení v produkčním prostředí.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Instalace démona zabezpečení IoT Edge

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Nakonfigurujte funkce zabezpečení Azure IoT Edge


Proces démon lze konfigurovat pomocí konfiguračního souboru na `/etc/iotedge/config.yaml`. Soubor je chráněn proti zápisu ve výchozím nastavení, bude pravděpodobně nutné zvýšenou úroveň oprávnění a upravte ji.

```bash
sudo nano /etc/iotedge/config.yaml
```

Hraniční zařízení můžete nakonfigurovat ručně pomocí [připojovací řetězec zařízení] [ lnk-dcs] nebo [automaticky prostřednictvím služby Azure Device Provisioning] [ lnk-dps].

* Ruční konfigurace, zrušte komentář **ruční** režim zřizování. Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem z vašeho zařízení IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Automatická konfigurace, zrušte komentář **dps** režim zřizování. Aktualizujte hodnoty **scope_id** a **registration_id** s hodnotami z vaší instance IoT Hub Device Provisioning a zařízení IoT Edge s čipem TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Jakmile zadáte informace o zřizování v konfiguraci, restartujte proces démon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud jste použili **ruční konfigurace** kroků v předchozí části, modul runtime IoT Edge by měla být úspěšně zřízený a spuštěný ve vašem zařízení. Pokud jste použili **automatickou konfiguraci** kroky, pak budete muset provést některé další kroky, tak, aby modul runtime můžete registraci zařízení ve službě IoT hub vaším jménem. Další pokyny najdete v článku [vytvoření a zřízení simulovaného zařízení TPM Edge na virtuální počítač s Linuxem](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Můžete zkontrolovat stav použití IoT Edge démon:

```cmd/sh
systemctl status iotedge
```

Zkontrolujte protokoly démonu pomocí:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

A seznam s moduly:

```cmd/sh
sudo iotedge list
```
>[!NOTE]
>U prostředku omezené zařízení, jako jsou raspberrypi nebo, který důrazně doporučujeme *OptimizeForPerformance* proměnná prostředí je nastavená na *false* podle pokynů v [ Průvodce odstraňováním potíží.][lnk-trouble]


## <a name="next-steps"></a>Další postup

Pokud máte problémy s modulu runtime Edge instalaci správně, rezervace [řešení potíží s] [ lnk-trouble] stránky.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
