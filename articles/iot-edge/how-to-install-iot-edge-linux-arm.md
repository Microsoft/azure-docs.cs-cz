---
title: Nainstalovat Azure IoT Edge pro Linux ARM32 | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na Linux na zařízeních ARM32, jako je například Malina PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224694"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalace modulu runtime Azure IoT Edge v systému Linux (ARM32v7/armhf)

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu. 

Další informace o fungování modul runtime IoT Edge a jaké součásti jsou zahrnuty, naleznete v tématu [pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge na zařízení IoT Edge se systémem Linux ARM32v7/armhf. Například tyto kroky budou fungovat pro zařízení malin PI. Seznam podporovaných operačních systémů ARM32 najdete v tématu [Azure IoT Edge podporovaných systémech](support.md#operating-systems). 

>[!NOTE]
>Balíčky v úložiště softwaru Linux jsou souladu s licenčními podmínkami v jednotlivých balíčků (/ usr/sdílet/doc/*název balíčku*). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

## <a name="install-the-latest-version"></a>Nainstalovat nejnovější verzi

K instalaci nejnovější verze Azure IoT Edge služby do zařízení se systémem Linux ARM použijte následující části. 

### <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním [OCI](https://www.opencontainers.org/) . Pro produkční scénáře, důrazně doporučujeme vám použít [na základě Moby](https://mobyproject.org/) níže uvedený modul. Je modul pouze container oficiálně podporované službou Azure IoT Edge. Image kontejnerů Docker CE/EE jsou kompatibilní s modulem runtime založeném na Moby.

Následující příkazy instalují modul založené na Moby i rozhraní příkazového řádku (CLI). Rozhraní příkazového řádku je užitečné pro vývoj, ale volitelný pro nasazení v produkčním prostředí.

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>Instalace démona zabezpečení IoT Edge

**Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Proces démon spustí při každé spuštění a bootstraps zařízení spuštěním rest modul runtime IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

Po úspěšné instalaci IoT Edge zobrazí se ve výstupu výzva k aktualizaci konfiguračního souboru. Postupujte podle kroků v části [konfigurace Azure IoT Edge démona zabezpečení](#configure-the-azure-iot-edge-security-daemon) a dokončete zřízení zařízení. 

## <a name="install-a-specific-version"></a>Instalace konkrétní verze

Pokud chcete nainstalovat určitou verzi Azure IoT Edge, můžete cílit na soubory součásti přímo z úložiště GitHub IoT Edge. Pomocí stejných `curl` příkazů uvedených v předchozích částech získáte všechny součásti IoT Edge do svého zařízení: modul Moby a rozhraní příkazového řádku, libiothsm a nakonec proces démona zabezpečení IoT Edge. Jediným rozdílem je, že nahradíte adresy URL **aka.MS** odkazy přímo odkazující na verzi každé součásti, kterou chcete použít.

Přejděte do vydaných verzí [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)a vyhledejte verzi pro vydání, na kterou chcete cílit. Rozbalte část **assets (prostředky** ) pro danou verzi a vyberte soubory, které odpovídají architektuře zařízení IoT Edge. Každá verze IoT Edge obsahuje soubory **iotedge** a **libiothsm** . Ne všechny verze zahrnují **Moby-Engine** nebo **Moby-CLI**. Pokud ještě nemáte nainstalovaný modul Moby Container Engine, Projděte si starší verze, dokud nenajdete nějaký, který obsahuje komponenty Moby. 

Po úspěšné instalaci IoT Edge zobrazí se ve výstupu výzva k aktualizaci konfiguračního souboru. Pokud chcete dokončit zřizování zařízení, postupujte podle kroků v následující části. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurace démona zabezpečení Azure IoT Edge

Konfigurace modulu runtime IoT Edge k propojení vaší fyzické zařízení s identitou zařízení, která existuje v Azure IoT hub. 

Proces démon lze konfigurovat pomocí konfiguračního souboru na `/etc/iotedge/config.yaml`. Soubor je ve výchozím nastavení chráněný proti zápisu, takže možná budete potřebovat zvýšená oprávnění k jeho úpravám.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení ke zřízení. Podle svého výběru: zřizování zvolte příslušný instalační skript. 

### <a name="option-1-manual-provisioning"></a>Možnost 1: Ruční zřizování

Pokud chcete zařízení zřídit ručně, musíte ho zadat pomocí [připojovacího řetězce zařízení](how-to-register-device-portal.md) , který můžete vytvořit tak, že zaregistrujete nové zařízení IoT Edge ve službě IoT Hub.

Otevřete konfigurační soubor. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte konfigurace zřizování souboru a odkomentujte oddíl **Ruční konfigurace zřizování** . Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem z vašeho zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

Uložte soubor a zavřete ho. 

`CTRL + X`, `Y`, `Enter`

Jakmile zadáte informace o zřizování v konfiguračním souboru, restartujte démona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Možnost 2: Automatické zřizování

K automatickému zřízení zařízení, [nastavení služby Device Provisioning a načtení ID registrace zařízení](how-to-auto-provision-simulated-device-linux.md). Pokud používáte Automatické zřizování, ale vaše požadavky na hardware mají vliv na řadu mechanismů ověřování, které IoT Edge podporuje. Například zařízení malinu PI ve výchozím nastavení nespadají do čipu TPM (Trusted Platform Module).

Otevřete konfigurační soubor. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte konfigurace zřizování souboru a zrušte komentář k oddílu, který je vhodný pro váš mechanismus ověřování. Při použití ověření identity pomocí čipu TPM například aktualizujte hodnoty **scope_id** a **registration_id** o hodnoty z vaší služby IoT Hub Device Provisioning a IoT Edge zařízení s čipem TPM.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

Uložte soubor a zavřete ho. 

`CTRL + X`, `Y`, `Enter`

Jakmile zadáte informace o zřizování v konfiguračním souboru, restartujte démona:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud jste použili **ruční konfigurace** kroků v předchozí části, modul runtime IoT Edge by měla být úspěšně zřízený a spuštěný ve vašem zařízení. Nebo pokud jste použili **automatické konfigurační** kroky, musíte provést několik dalších kroků, aby modul runtime mohl vaše zařízení zaregistrovat vaším jménem vaší služby IoT Hub. Další postup najdete v tématu [Vytvoření a zřízení simulovaného zařízení TPM IoT Edge na virtuálním počítači se systémem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Na zařízeních prostředků omezené, důrazně doporučujeme, abyste nastavili *OptimizeForPerformance* proměnnou prostředí, aby *false* podle pokynů v [Průvodce odstraňováním potíží ](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Pokud vaše síť, která má proxy server, postupujte podle kroků v [nakonfigurujte zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Odinstalace IoT Edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení se systémem Linux, použijte následující příkazy z příkazového řádku. 

Odeberte modul runtime IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Po odebrání IoT Edge modul runtime se zastaví kontejner, který byl vytvořen, ale stále existují na vašem zařízení. Zobrazením všech kontejnerů zjistíte, které z nich zůstanou. 

```bash
sudo docker ps -a
```

Odstraňte kontejnery ze zařízení včetně dvou kontejnerů modulu runtime. 

```bash
sudo docker rm -f <container name>
```

Nakonec odeberte modul runtime kontejneru z vašeho zařízení. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Další postup

Teď, když máte zařízení IoT Edge zřízené s modulem runtime nainstalovaný, je možné [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací modulu runtime IoT Edge správně, přečtěte si stránku [Poradce při potížích](troubleshoot.md#stability-issues-on-resource-constrained-devices) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
