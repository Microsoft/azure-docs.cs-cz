---
title: Postup instalace Azure IoT Edge v systému Linux | Microsoft Docs
description: Azure IoT Edge instalační pokyny v systému Linux na ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062594"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalace modulu runtime Azure IoT Edge na platformě Linux (ARM32v7/armhf)

Modul runtime Azure IoT okraj je nasadit na všechna zařízení IoT okraj. Má tři součásti. **IoT Edge zabezpečení démon** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Démon se spustí na každém spuštění a bootstraps zařízení spuštěním agenta IoT okraj. **IoT Edge agenta** usnadňuje nasazení a monitorování modulů na hraniční zařízení, včetně IoT Edge hub. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Tento článek obsahuje kroky k instalaci modulu runtime Azure IoT Edge na Linux ARM32v7/armhf hraniční zařízení (například malin pí).

>[!NOTE]
>Balíčky v úložiště softwaru Linux jsou souladu s licenčními podmínkami, který je umístěný v jednotlivých balíčků (/ usr/sdílet/doc/*název balíčku*). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku, znamená to, že přijímáte tyto podmínky. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíčku.

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

Azure IoT Edge spoléhá na [OCI kompatibilní] [ lnk-oci] kontejner modulu runtime (např. Docker). Pokud již máte Docker CE nebo EE nainstalované na hraniční zařízení, můžete nadále používat pro vývoj a testování pomocí Azure IoT okraj. 

Pro produkční scénáře, důrazně doporučujeme použijete [na základě Moby] [ lnk-moby] modul níže uvedenou. Je modul pouze kontejneru oficiálně podporované s Azure IoT hranou. Docker CE nebo EE kontejneru Image jsou plně kompatibilní s modulem runtime Moby.

Níže uvedené příkazy nainstalovat modul moby a rozhraní příkazového řádku (CLI). Rozhraní příkazového řádku je užitečné pro vývoj, ale volitelné pro nasazení v produkčním prostředí.

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

## <a name="install-the-iot-edge-security-daemon"></a>Instalace démona zabezpečení hraniční IoT

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

Démon lze nakonfigurovat pomocí konfiguračního souboru na `/etc/iotedge/config.yaml` hraniční zařízení je možné nakonfigurovat <!--[automatically via Device Provisioning Service][lnk-dps] or--> ručně pomocí [zařízení připojovací řetězec][lnk-dcs].

Pro ruční konfigurace, zadejte připojovací řetězec zařízení **zřizování** části **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Soubor je chráněna proti zápisu ve výchozím nastavení, bude pravděpodobně nutné použít `sudo` jej upravit. Například `sudo nano /etc/iotedge/config.yaml`*

Po zadání informace o zřizování v konfiguraci, restartujte démon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Můžete zkontrolovat stav démon Edge IoT pomocí:

```cmd/sh
systemctl status iotedge
```

Zkontrolujte démon protokolů pomocí:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

A seznam systémem modulů s:

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>Další postup

Pokud máte potíže s modulem runtime Edge instalaci správně, najdete v článku věnovaném [řešení potíží s] [ lnk-trouble] stránky.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md