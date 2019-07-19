---
title: Instalace Azure IoT Edge v Linuxu | Dokumentace Microsoftu
description: Instalační pokyny pro Azure IoT Edge v Linuxu AMD64 zařízení se systémem Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 822efe2534d49c0995a672232107cc322e547989
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227514"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalace modulu runtime Azure IoT Edge v Linuxu (x64)

Modul runtime Azure IoT Edge je co se změní na zařízení do zařízení IoT Edge. Modul runtime můžete nasadit na zařízení jako Raspberry Pi malé nebo velké průmyslové serveru. Jakmile je zařízení nakonfigurovaná s modulem runtime IoT Edge, můžete začít nasazovat obchodní logiky k němu z cloudu.

Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge na Ubuntu Linux x64 (Intel/AMD) IoT Edge zařízení. Seznam podporovaných operačních systémů AMD64 najdete v tématu [Azure IoT Edge podporované systémy](support.md#operating-systems) .

> [!NOTE]
> Balíčky v úložiště softwaru Linux jsou souladu s licenčními podmínkami v jednotlivých balíčků (/ usr/sdílet/doc/*název balíčku*). Přečtěte si licenční podmínky před použitím balíčku. Instalace a použití balíčku se považuje za svůj souhlas s těmito podmínkami. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

## <a name="install-the-latest-version"></a>Nainstalovat nejnovější verzi

K instalaci nejnovější verze Azure IoT Edge služby do zařízení použijte následující části. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Zaregistrujte informační kanál úložiště Microsoft klíč a softwaru

Připravte zařízení na instalaci modulu runtime IoT Edge.


Nainstalujte konfiguraci úložiště. Vyberte buď fragment kódu **16,04** nebo **18,04** , který je vhodný pro vaši verzi Ubuntu:

> [!NOTE]
> Ujistěte se, že jste vybrali fragment kódu ze správného pole kódu pro vaši verzi Ubuntu.

* Pro **Ubuntu 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* Pro **Ubuntu 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
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

### <a name="install-the-container-runtime"></a>Nainstalovat modul runtime kontejneru

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním [OCI](https://www.opencontainers.org/) . V produkčních scénářích doporučujeme použít modul založený na [Moby](https://mobyproject.org/) , který je uvedený níže. Je modul pouze container oficiálně podporované službou Azure IoT Edge. Jsou kompatibilní s modulem runtime Moby imagí kontejnerů dockeru CE/EE.

Proveďte aktualizaci apt.

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

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Ověření kompatibility se systémem Linux v jádře Moby

Řada integrovaných výrobců zařízení dodává obrázky zařízení, které obsahují vlastní jádra systému Linux, ve kterých mohou chybět funkce vyžadované pro kompatibilitu s modulem runtime kontejneru. Pokud narazíte na problémy při instalaci doporučeného modulu runtime [Moby](https://github.com/moby/moby) Container, možná budete moci vyřešit potíže s konfigurací jádra pro Linux pomocí skriptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) , který je součástí oficiálního [úložiště GitHub Moby](https://github.com/moby/moby) . na zařízení spustíte následující příkazy.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Tím se poskytne podrobný výstup, který obsahuje stav funkcí jádra používaných modulem runtime Moby. Budete chtít zajistit, aby všechny položky v `Generally Necessary` a `Network Drivers` byly povoleny, aby bylo zajištěno, že je jádro plně kompatibilní s modulem runtime Moby.  Pokud jste identifikovali chybějící funkce, můžete je povolit tak, že znovu sestavíte jádro ze zdroje a vyberete přidružené moduly pro zahrnutí do příslušného jádra. config.  Podobně pokud používáte generátor konfigurace jádra, jako je defconfig nebo menuconfig, bude nutné vyhledat a povolit příslušné funkce a odpovídajícím způsobem sestavit jádro.  Po nasazení nově upraveného jádra spusťte skript check-config znovu, abyste ověřili, že vaše identifikované funkce byly úspěšně povoleny.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

**Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Proces démon spustí při každé spuštění a bootstraps zařízení spuštěním rest modul runtime IoT Edge.

Instalační příkaz nainstaluje také standardní verzi **libiothsm** , pokud ještě není k dispozici.

Proveďte aktualizaci apt.

   ```bash
   sudo apt-get update
   ```

Instalace démona zabezpečení. Při instalaci balíčku `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Po úspěšné instalaci IoT Edge zobrazí se ve výstupu výzva k aktualizaci konfiguračního souboru. Postupujte podle kroků v části [konfigurace Azure IoT Edge démona zabezpečení](#configure-the-azure-iot-edge-security-daemon) a dokončete zřízení zařízení. 

## <a name="install-a-specific-version"></a>Instalace konkrétní verze

Pokud chcete nainstalovat určitou verzi Azure IoT Edge, můžete cílit na soubory součásti přímo z úložiště GitHub IoT Edge. Pomocí následujících kroků načtete všechny součásti IoT Edge do svého zařízení: modul Moby a rozhraní příkazového řádku, libiothsm a nakonec proces démona zabezpečení IoT Edge.

1. Přejděte do vydaných verzí [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)a vyhledejte verzi pro vydání, na kterou chcete cílit. 

2. Rozbalte část **assets (prostředky** ) pro danou verzi.

3. V žádné dané vydané verzi může nebo nemusí být možné aktualizovat modul Moby. Pokud se zobrazí soubory, které začínají na **Moby** a **Moby-CLI**, aktualizujte tyto součásti pomocí následujících příkazů. Pokud nevidíte žádné soubory Moby a ještě nemáte na svém zařízení nainstalované Moby, vraťte se k předchozímu assetu vydané verze, dokud je nenajdete. 

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

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurace démona zabezpečení Azure IoT Edge

Konfigurace modulu runtime IoT Edge k propojení vaší fyzické zařízení s identitou zařízení, která existuje v Azure IoT hub.

Proces démon lze konfigurovat pomocí konfiguračního souboru na `/etc/iotedge/config.yaml`. Soubor je chráněn proti zápisu ve výchozím nastavení, bude pravděpodobně nutné zvýšenou úroveň oprávnění a upravte ji.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení k dispozici ve službě IoT Hub. Nebo můžete do služby Device Provisioning k automatickému zřízení zařízení, což je užitečné, když máte velký počet zařízení ke zřízení. Podle svého výběru: zřizování zvolte příslušný instalační skript.

### <a name="option-1-manual-provisioning"></a>Možnost 1: Ruční zřizování

Ruční zřizování zařízení, budete muset zadat ho [připojovací řetězec zařízení](how-to-register-device-portal.md) , můžete vytvořit tak, že zaregistrujete nového zařízení ve službě IoT hub.

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

Pokud jste použili **ruční konfigurace** kroků v předchozí části, modul runtime IoT Edge by měla být úspěšně zřízený a spuštěný ve vašem zařízení. Pokud jste použili **automatickou konfiguraci** kroky, pak budete muset provést některé další kroky, tak, aby modul runtime můžete registraci zařízení ve službě IoT hub vaším jménem. Další postup najdete v tématu [Vytvoření a zřízení simulovaného zařízení TPM IoT Edge na virtuálním počítači se systémem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Pokud máte problémy s instalací modulu runtime IoT Edge správně, podívejte se na stránku [Poradce při potížích](troubleshoot.md) .

Chcete-li aktualizovat existující instalaci na nejnovější verzi IoT Edge, přečtěte si téma [aktualizace procesu démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).
