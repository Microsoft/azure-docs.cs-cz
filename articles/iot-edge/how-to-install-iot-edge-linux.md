---
title: Nainstalovat Azure IoT Edge pro Linux | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na zařízeních se systémem Linux se systémem Ubuntu nebo Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: aca417ebbc6f9af80058ddece32842f38918ce60
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964764"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalace modulu runtime Azure IoT Edge v systémech Linux založených na Debian

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile je zařízení nakonfigurované s modulem runtime IoT Edge, můžete do něj začít nasazovat obchodní logiku z cloudu. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge na zařízení s platformou x64, ARM32 nebo ARM64 Linux. Instalační balíčky jsou k dispozici pro Ubuntu Server 16,04, Ubuntu Server 18,04 a Raspbian Stretch. Seznam podporovaných operačních systémů a architektur pro Linux najdete v tématu [Azure IoT Edge podporované systémy](support.md#operating-systems) .

>[!NOTE]
>Podpora pro zařízení ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Balíčky v úložištích softwaru Linux podléhají licenčním podmínkám, které jsou umístěny v každém balíčku (/usr/share/doc/*název balíčku*). Přečtěte si licenčních podmínek před použitím balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

## <a name="install-the-latest-runtime-version"></a>Nainstalovat nejnovější verzi modulu runtime

Pomocí následujících částí můžete nainstalovat nejnovější verzi Azure IoT Edge runtime do svého zařízení. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrovat klíč úložiště klíčů Microsoftu a softwaru

Připravte zařízení na instalaci modulu runtime IoT Edge.

Nainstalujte konfiguraci úložiště. Vyberte příkaz **16,04** nebo **18,04** , který odpovídá operačnímu systému zařízení:

* **Ubuntu Server 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Zkopírujte vygenerovaný seznam.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Nainstalovat veřejný klíč Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instalace modulu runtime kontejneru

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním [OCI](https://www.opencontainers.org/) . V produkčních scénářích doporučujeme použít modul založený na [Moby](https://mobyproject.org/) , který je uvedený níže. Jediným kontejnerovým modulem se Azure IoT Edge oficiálně podporuje. Image kontejnerů Docker CE/EE jsou kompatibilní s modulem runtime Moby.

Proveďte aktualizaci apt.

   ```bash
   sudo apt-get update
   ```

Nainstalujte modul Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Nainstalujte rozhraní příkazového řádku Moby (CLI). Rozhraní příkazového řádku je užitečné pro vývoj, ale volitelné pro produkční nasazení.

   ```bash
   sudo apt-get install moby-cli
   ```

Pokud narazíte na chyby při instalaci modulu runtime kontejneru Moby, postupujte podle pokynů pro [ověření jádra systému Linux pro kompatibilitu s Moby](#verify-your-linux-kernel-for-moby-compatibility), jak je uvedeno dále v tomto článku. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

**Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Démon procesu se spustí při každém spuštění a spustí zařízení spuštěním zbytku modulu runtime IoT Edge.

Instalační příkaz nainstaluje také standardní verzi **libiothsm** , pokud ještě není k dispozici.

Proveďte aktualizaci apt.

   ```bash
   sudo apt-get update
   ```

Nainstalujte démona zabezpečení. Balíček je nainstalován na `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Po úspěšné instalaci IoT Edge zobrazí se ve výstupu výzva k aktualizaci konfiguračního souboru. Postupujte podle kroků v části [konfigurace Azure IoT Edge démona zabezpečení](#configure-the-security-daemon) a dokončete zřízení zařízení. 

## <a name="install-a-specific-runtime-version"></a>Instalace konkrétní verze modulu runtime

Pokud chcete nainstalovat určitou verzi Azure IoT Edge runtime, můžete cílit na soubory součásti přímo z úložiště GitHub IoT Edge. Pomocí následujících kroků načtete všechny součásti IoT Edge do svého zařízení: modul Moby a rozhraní příkazového řádku, libiothsm a nakonec proces démona zabezpečení IoT Edge.

1. Přejděte do vydaných verzí [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)a vyhledejte verzi pro vydání, na kterou chcete cílit. 

2. Rozbalte část **assets (prostředky** ) pro danou verzi.

3. V žádné dané vydané verzi může nebo nemusí být možné aktualizovat modul Moby. Pokud se zobrazí soubory, které začínají na **Moby** a **Moby-CLI**, aktualizujte tyto součásti pomocí následujících příkazů. Pokud nevidíte žádné soubory Moby, přečtěte si předchozí assety vydaných verzí, dokud nenajdete nejnovější verzi. 

   1. Vyhledejte soubor **Moby** , který odpovídá architektuře zařízení IoT Edge. Klikněte pravým tlačítkem na odkaz na soubor a zkopírujte adresu odkazu.

   2. Pomocí zkopírovaného odkazu v následujícím příkazu nainstalujte tuto verzi modulu Moby: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Vyhledejte soubor **Moby-CLI** , který odpovídá architektuře zařízení IoT Edge. Moby CLI je volitelná součást, ale může být užitečná během vývoje. Klikněte pravým tlačítkem na odkaz na soubor a zkopírujte adresu odkazu. 

   4. Pomocí zkopírovaného odkazu v následujícím příkazu nainstalujte tuto verzi rozhraní příkazového řádku Moby: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Každé vydání by mělo mít nové soubory pro démona zabezpečení IoT Edge a hsmlib. Tyto součásti můžete aktualizovat pomocí následujících příkazů. 

   1. Vyhledejte soubor **libiothsm-STD** , který odpovídá architektuře zařízení IoT Edge. Klikněte pravým tlačítkem na odkaz na soubor a zkopírujte adresu odkazu. 

   2. Pomocí zkopírovaného odkazu v následujícím příkazu nainstalujte tuto verzi nástroje hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Vyhledejte soubor **iotedge** , který odpovídá architektuře zařízení IoT Edge. Klikněte pravým tlačítkem na odkaz na soubor a zkopírujte adresu odkazu. 

   4. Pomocí zkopírovaného odkazu v následujícím příkazu nainstalujte tuto verzi procesu démona zabezpečení IoT Edge. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Po úspěšné instalaci IoT Edge zobrazí se ve výstupu výzva k aktualizaci konfiguračního souboru. Pokud chcete dokončit zřizování zařízení, postupujte podle kroků v následující části. 

## <a name="configure-the-security-daemon"></a>Konfigurace démona zabezpečení

Nakonfigurujte modul runtime IoT Edge pro propojení fyzického zařízení s identitou zařízení, která existuje ve službě Azure IoT Hub.

Démona se dá nakonfigurovat pomocí konfiguračního souboru na `/etc/iotedge/config.yaml`. Soubor je ve výchozím nastavení chráněný proti zápisu, možná budete potřebovat zvýšená oprávnění k jeho úpravám.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení, který poskytuje IoT Hub. Nebo můžete službu Device Provisioning použít k automatickému zřízení zařízení, což je užitečné v případě, že je potřeba zřídit mnoho zařízení. V závislosti na výběru zřizování zvolte vhodný instalační skript.

### <a name="option-1-manual-provisioning"></a>Možnost 1: ruční zřizování

Pokud chcete zařízení zřídit ručně, musíte ho zadat pomocí [připojovacího řetězce zařízení](how-to-register-device.md#register-in-the-azure-portal) , který můžete vytvořit tak, že zaregistrujete nové zařízení ve službě IoT Hub.

Otevřete konfigurační soubor.

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte konfigurace zřizování souboru a odkomentujte oddíl **Ruční konfigurace zřizování** . Aktualizujte hodnotu **device_connection_string** pomocí připojovacího řetězce ze zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány.

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
Pokud chcete vložit obsah schránky do nano `Shift+Right Click` nebo stiskněte `Shift+Insert`.

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Možnost 2: Automatické zřizování

Pokud chcete automaticky zřídit zařízení, [nastavte službu Device Provisioning a načtěte ID registrace zařízení](how-to-auto-provision-simulated-device-linux.md). Pokud používáte Automatické zřizování, ale vaše požadavky na hardware mají vliv na řadu mechanismů ověřování, které IoT Edge podporuje. Například zařízení malinu PI ve výchozím nastavení nespadají do čipu TPM (Trusted Platform Module).

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

Pokud chcete vložit obsah schránky do nano `Shift+Right Click` nebo stiskněte `Shift+Insert`.

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud jste použili kroky **Ruční konfigurace** v předchozí části, modul runtime IoT Edge by měl být na vašem zařízení úspěšně zřízený a spuštěný. Pokud jste použili kroky pro **automatické konfigurace** , budete muset provést některé další kroky, aby modul runtime mohl vaše zařízení zaregistrovat vaším jménem vaší služby IoT Hub. Další postup najdete v tématu [Vytvoření a zřízení simulovaného zařízení TPM IoT Edge na virtuálním počítači se systémem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Můžete kontrolovat stav procesu démona IoT Edge:

```bash
systemctl status iotedge
```

Kontrola protokolů démona:

```bash
journalctl -u iotedge --no-pager --no-full
```

Spusťte automatizovanou kontrolu nejběžnějších chyb konfigurace a sítě: 

```bash
sudo iotedge check
```

A, vypsat běžící moduly:

```bash
sudo iotedge list
```

Jediným modulem, který by měl být spuštěný, je po instalaci IoT Edge na zařízení **edgeAgent**. Po vytvoření prvního nasazení se v zařízení spustí i jiný systémový modul **$edgeHub** . Další informace najdete v tématu [nasazení IoT Edgech modulů](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Tipy a řešení potíží

Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Po instalaci modulu runtime se odhlaste z počítače a znovu se přihlaste, abyste mohli aktualizovat vaše oprávnění automaticky. Do té doby použijte **sudo** před jakýmkoli `iotedge` příkazů.

U zařízení s omezeným prostředkem se důrazně doporučuje nastavit proměnnou prostředí *OptimizeForPerformance* na *hodnotu false* podle pokynů v [Průvodci odstraňováním potíží](troubleshoot.md).

Pokud vaše síť obsahuje proxy server, postupujte podle kroků v části [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Ověření kompatibility se systémem Linux v jádře Moby

Řada integrovaných výrobců zařízení dodává obrázky zařízení, které obsahují vlastní jádra systému Linux bez funkcí požadovaných pro kompatibilitu s modulem runtime kontejneru. Pokud narazíte na problémy při instalaci doporučeného modulu runtime Moby Container, možná budete moci vyřešit potíže s konfigurací jádra pro Linux pomocí skriptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) z oficiálního [úložiště GitHub Moby](https://github.com/moby/moby). Spuštěním následujících příkazů na zařízení ověřte konfiguraci jádra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Tím se poskytne podrobný výstup, který obsahuje stav funkcí jádra, které používá modul runtime Moby. Budete chtít zajistit, aby všechny položky v části `Generally Necessary` a `Network Drivers` byly povoleny, aby bylo zajištěno, že je jádro plně kompatibilní s modulem runtime Moby.  Pokud jste identifikovali chybějící funkce, povolte je opětovným sestavením jádra ze zdroje a výběrem přidružených modulů pro zahrnutí do příslušného souboru kernel. config.  Podobně pokud používáte generátor konfigurace jádra, jako je defconfig nebo menuconfig, najděte a povolte příslušné funkce a odpovídajícím způsobem Sestavte jádro.  Po nasazení nově upraveného jádra spusťte skript check-config znovu, abyste ověřili, že všechny požadované funkce byly úspěšně povoleny.


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

## <a name="next-steps"></a>Další kroky

Teď, když máte nainstalovanou IoT Edge zařízení s nainstalovaným modulem runtime, můžete [nasadit IoT Edge moduly](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací modulu runtime IoT Edge správně, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
