---
title: Postup instalace Azure IoT Edge v systému Linux | Microsoft Docs
description: Azure IoT Edge instalační pokyny v systému Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 273bb920b1ef2cac425ba9e636d488c8219ad9d2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37126962"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalace modulu runtime Azure IoT Edge na platformě Linux (x64)

Modul runtime Azure IoT okraj je nasadit na všechna zařízení IoT okraj. Má tři součásti. **IoT Edge zabezpečení démon** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Démon se spustí na každém spuštění a bootstraps zařízení spuštěním agenta IoT okraj. **IoT Edge agenta** usnadňuje nasazení a monitorování modulů na hraniční zařízení, včetně IoT Edge hub. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Tento článek obsahuje kroky k instalaci modulu runtime Azure IoT Edge na vašem systému Linux x64 (Intel nebo AMD) hraniční zařízení.

>[!NOTE]
>Balíčky v úložiště softwaru Linux jsou souladu s licenčními podmínkami, který je umístěný v jednotlivých balíčků (/ usr/sdílet/doc/*název balíčku*). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku, znamená to, že přijímáte tyto podmínky. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíčku.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Zaregistrovat informační kanál úložiště Microsoft klíč a softwaru

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru 

Azure IoT Edge spoléhá na [OCI kompatibilní] [ lnk-oci] kontejner modulu runtime (např. Docker). Pokud již máte Docker CE nebo EE nainstalované na hraniční zařízení, můžete nadále používat pro vývoj a testování pomocí Azure IoT okraj. 

Pro produkční scénáře, důrazně doporučujeme použijete [na základě Moby] [ lnk-moby] modul níže uvedenou. Je modul pouze kontejneru oficiálně podporované s Azure IoT hranou. Docker CE nebo EE kontejneru Image jsou plně kompatibilní s modulem runtime Moby.

*Podle následujících pokynů nainstalujte modul moby a rozhraní příkazového řádku (CLI). Rozhraní příkazového řádku je užitečné pro vývoj, ale volitelné pro nasazení v produkčním prostředí.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

Níže uvedené příkazy také nainstaluje standardní verzi systému **iothsmlib** Pokud již nejsou přítomny.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
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
sudo iotedge list
```

## <a name="next-steps"></a>Další postup

Pokud máte potíže s modulem runtime Edge instalaci správně, najdete v článku věnovaném [řešení potíží s] [ lnk-trouble] stránky.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
