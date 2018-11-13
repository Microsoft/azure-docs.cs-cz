---
title: Jak nainstalovat Azure IoT Edge v Linuxu | Dokumentace Microsoftu
description: Instalační pokyny pro Azure IoT Edge v Linuxu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 08946076add9ab1c0972729fa89cf8aea0968c99
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568502"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalace modulu runtime Azure IoT Edge v Linuxu (x64)

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu. 

Další informace o fungování modul runtime IoT Edge a jaké součásti jsou zahrnuty, naleznete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky instalace modulu runtime Azure IoT Edge na vaší platformě Linux x64 (Intel nebo AMD) hraniční zařízení. Odkazovat na [podpory Azure IoT Edge](support.md#operating-systems) seznam AMD64 operační systémy, které jsou aktuálně podporovány. 

>[!NOTE]
>Balíčky v úložiště softwaru Linux jsou souladu s licenčními podmínkami v jednotlivých balíčků (/ usr/sdílet/doc/*název balíčku*). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Zaregistrujte informační kanál úložiště Microsoft klíč a softwaru

V závislosti na operačním systému zvolte příslušné skripty k přípravě vašeho zařízení IoT Edge instalace modulu runtime. 

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru 

Azure IoT Edge se může spolehnout [OCI kompatibilní](https://www.opencontainers.org/) kontejner modulu runtime. Pro produkční scénáře, důrazně doporučujeme vám použít [na základě Moby](https://mobyproject.org/) níže uvedený modul. Je modul pouze container oficiálně podporované službou Azure IoT Edge. Jsou kompatibilní s modulem runtime Moby imagí kontejnerů dockeru CE/EE.

Aktualizujte apt-get.

```bash
sudo apt-get update
```

Nainstalujte modul Moby. 

```bash
sudo apt-get install moby-engine
```

Nainstalujte Moby rozhraní příkazového řádku (CLI). Rozhraní příkazového řádku je užitečné pro vývoj, ale volitelný pro nasazení v produkčním prostředí.

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

**Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na hraniční zařízení. Proces démon spustí při každé spuštění a bootstraps zařízení spuštěním rest modul runtime IoT Edge. 

Instalační příkaz nainstaluje také standardní verze **iothsmlib** Pokud není již k dispozici.

Aktualizujte apt-get.

```bash
sudo apt-get update
```

Instalace démona zabezpečení. Při instalaci balíčku `/etc/iotedge/`.

```bash
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Nakonfigurujte funkce zabezpečení Azure IoT Edge

Konfigurace modulu runtime IoT Edge k propojení vaší fyzické zařízení s identitou zařízení, která existuje v Azure IoT hub. 

Proces démon lze konfigurovat pomocí konfiguračního souboru na `/etc/iotedge/config.yaml`. Soubor je chráněn proti zápisu ve výchozím nastavení, bude pravděpodobně nutné zvýšenou úroveň oprávnění a upravte ji.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení ke zřízení. Podle svého výběru: zřizování zvolte příslušný instalační skript. 

### <a name="option-1-manual-provisioning"></a>Možnost 1: Ruční zřizování

Ruční zřizování zařízení, budete muset zadat ho [připojovací řetězec zařízení](how-to-register-device-portal.md) , můžete vytvořit tak, že zaregistrujete nového zařízení ve službě IoT hub.


Otevřete konfigurační soubor. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte část souboru zřizování a zrušte komentář **ruční** režim zřizování. Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem z vašeho zařízení IoT Edge.

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

Uložte soubor a zavřete ho. 

   `CTRL + X`, `Y`, `Enter`

Jakmile zadáte informace o zřizování v konfiguračním souboru, restartujte démona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Možnost 2: Automatické zřizování

K automatickému zřízení zařízení, [nastavení služby Device Provisioning a načtení ID registrace zařízení](how-to-auto-provision-simulated-device-linux.md). Automatické zřizování funguje jenom u zařízení, která mají čipu Trusted Platform Module (TPM). Například zařízení Raspberry Pi nezahrnují TPM ve výchozím nastavení. 

Otevřete konfigurační soubor. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte část souboru zřizování a zrušte komentář **dps** režim zřizování. Aktualizujte hodnoty **scope_id** a **registration_id** s hodnotami z vašeho IoT Hub Device Provisioning Service a zařízení IoT Edge s čipem TPM. 

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

Uložte soubor a zavřete ho. 

   `CTRL + X`, `Y`, `Enter`

Jakmile zadáte informace o zřizování v konfiguračním souboru, restartujte démona:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud jste použili **ruční konfigurace** kroků v předchozí části, modul runtime IoT Edge by měla být úspěšně zřízený a spuštěný ve vašem zařízení. Pokud jste použili **automatickou konfiguraci** kroky, pak budete muset provést některé další kroky, tak, aby modul runtime můžete registraci zařízení ve službě IoT hub vaším jménem. Další pokyny najdete v článku [vytvoření a zřízení simulovaného zařízení TPM Edge na virtuální počítač s Linuxem](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Můžete zkontrolovat stav použití IoT Edge démon:

```bash
systemctl status iotedge
```

Zkontrolujte protokoly démonu pomocí:

```bash
journalctl -u iotedge --no-pager --no-full
```

A seznam s moduly:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Tipy a návrhy

Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Po instalaci modulu runtime, odhlaste se z vašeho počítače a znovu se přihlaste automaticky aktualizovat vaše oprávnění. Dokud to neuděláte, použijte **sudo** před všechny `iotedge` příkazy.

Na zařízeních prostředků omezené, důrazně doporučujeme, abyste nastavili *OptimizeForPerformance* proměnnou prostředí, aby *false* podle pokynů v [Průvodce odstraňováním potíží ](troubleshoot.md).

Pokud vaše síť, která má proxy server, postupujte podle kroků v [nakonfigurujte zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

## <a name="next-steps"></a>Další postup

Pokud máte problémy s modulu runtime Edge instalaci správně, podívejte se [řešení potíží s](troubleshoot.md) stránky.

