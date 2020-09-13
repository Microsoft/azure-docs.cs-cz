---
title: Nainstalovat Azure IoT Edge pro Linux | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na zařízeních se systémem Linux se systémem Ubuntu nebo Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: kgremban
ms.openlocfilehash: 21fde76dc5791030a7afa280e00642119cbe464c
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660044"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalace modulu runtime Azure IoT Edge v systémech Linux založených na distribuci Debian

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge na zařízení s platformou x64, ARM32 nebo ARM64 Linux. Poskytujeme instalační balíčky pro server Ubuntu 16,04, Ubuntu Server 18,04 a Raspbian Stretch. Seznam podporovaných operačních systémů a architektur pro Linux najdete v tématu [Azure IoT Edge podporované systémy](support.md#operating-systems) .

> [!NOTE]
> Balíčky v úložištích softwaru Linux podléhají licenčním podmínkám, které jsou umístěny v každém balíčku (/usr/share/doc/*název balíčku*). Přečtěte si licenčních podmínek před použitím balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte balíček.

## <a name="install-container-runtime-and-iot-edge"></a>Instalace modulu runtime kontejneru a IoT Edge

Pomocí následujících částí můžete nainstalovat nejnovější verzi Azure IoT Edge runtime do svého zařízení.

>[!NOTE]
>Podpora pro zařízení ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Nainstalujte veřejný klíč Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instalace kontejnerového modulu runtime

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním [OCI](https://www.opencontainers.org/) . V produkčních scénářích doporučujeme použít modul založený na [Moby](https://mobyproject.org/) , který je uvedený níže. Modul Moby je jediným kontejnerovým modulem, který je oficiálně podporován s Azure IoT Edge. Image kontejnerů Docker CE/EE jsou kompatibilní s modulem runtime Moby.

Aktualizuje seznamy balíčků na vašem zařízení.

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

Pokud při instalaci modulu runtime kontejneru Moby dojde k chybám, postupujte podle kroků a [Ověřte, zda jádro systému Linux pro kompatibilitu s Moby](#verify-your-linux-kernel-for-moby-compatibility)je uvedeno dále v tomto článku.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace démona zabezpečení Azure IoT Edge

**Démon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Démon procesu se spustí při každém spuštění a spustí zařízení spuštěním zbytku modulu runtime IoT Edge.

Aktualizuje seznamy balíčků na vašem zařízení.

   ```bash
   sudo apt-get update
   ```

Zkontrolujte, které verze IoT Edge jsou k dispozici.

   ```bash
   apt list -a iotedge
   ```

Pokud chcete nainstalovat nejnovější verzi démona zabezpečení, použijte následující příkaz, který nainstaluje také nejnovější verzi balíčku **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Pokud chcete nainstalovat určitou verzi démona zabezpečení, zadejte verzi z výstupu seznamu apt. Zadejte také stejnou verzi balíčku **libiothsm-STD** , která by jinak instalovala nejnovější verzi. Například následující příkaz nainstaluje nejnovější verzi 1.0.8 verze:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Pokud verze, kterou chcete nainstalovat, není uvedená v seznamu, postupujte podle kroků v části [Instalace modulu runtime s použitím prostředků](#install-runtime-using-release-assets)vydaných verzí. V této části se dozvíte, jak cílit na předchozí verzi IoT Edge démona zabezpečení nebo Release Candidate verze.

Po úspěšné instalaci IoT Edge na `/etc/iotedge/` výstupu se zobrazí výzva k aktualizaci konfiguračního souboru. Pokračujte k další části a dokončete zřizování zařízení.

## <a name="configure-the-security-daemon"></a>Konfigurace démona zabezpečení

Nakonfigurujte modul runtime IoT Edge pro propojení fyzického zařízení s identitou zařízení, která existuje ve službě Azure IoT Hub.

Démona se dá nakonfigurovat pomocí konfiguračního souboru v `/etc/iotedge/config.yaml` . Soubor je ve výchozím nastavení chráněný proti zápisu, možná budete potřebovat zvýšená oprávnění k jeho úpravám.

Jedno zařízení IoT Edge se dá zřídit ručně pomocí řetězce připojení zařízení, který poskytuje IoT Hub. Nebo můžete službu Device Provisioning použít k automatickému zřízení zařízení, což je užitečné v případě, že je potřeba zřídit mnoho zařízení. V závislosti na výběru zřizování zvolte vhodný instalační skript.

### <a name="option-1-manual-provisioning"></a>Možnost 1: ruční zřizování

Pokud chcete zařízení zřídit ručně, musíte ho zadat pomocí [připojovacího řetězce zařízení](how-to-register-device.md#register-in-the-azure-portal) , který můžete vytvořit tak, že zaregistrujete nové zařízení ve službě IoT Hub.

Otevřete konfigurační soubor.

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte konfigurace zřizování souboru a odkomentujte oddíl **Ruční konfigurace zřizování** . Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem ze zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Vložení obsahu schránky do nano `Shift+Right Click` nebo stisknutím klávesy `Shift+Insert` .

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Možnost 2: Automatické zřizování

Zařízení IoT Edge se dají automaticky zřídit pomocí [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml). V současné době IoT Edge podporuje tři mechanismy ověřování při použití automatického zřizování, ale vaše hardwarové požadavky můžou mít vliv na vaše volby. Například zařízení malinu PI ve výchozím nastavení nespadají do čipu TPM (Trusted Platform Module). Další informace najdete v následujících článcích:

* [Vytvoření a zřízení zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači se systémem Linux](how-to-auto-provision-simulated-device-linux.md)
* [Vytvoření a zřízení zařízení IoT Edge pomocí certifikátů X. 509](how-to-auto-provision-x509-certs.md)
* [Vytvoření a zřízení zařízení IoT Edge pomocí ověřování symetrického klíče](how-to-auto-provision-symmetric-keys.md)

Tyto články vás provedou nastavením registrace v DPS a vygenerováním správných certifikátů nebo klíčů pro ověření identity. Bez ohledu na to, který mechanismus ověřování zvolíte, se informace o zřizování přidají do konfiguračního souboru IoT Edge na zařízení IoT Edge.

Otevřete konfigurační soubor.

```bash
sudo nano /etc/iotedge/config.yaml
```

Vyhledejte konfigurace zřizování souboru a zrušte komentář k oddílu, který je vhodný pro váš mechanismus ověřování. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. **Zřizování:** řádek by neměl mít žádné předchozí prázdné znaky a vnořené položky by měly být odsazeny dvěma mezerami. Aktualizujte hodnotu **scope_id** hodnotou z vaší instance IoT Hub Device Provisioning Service a zadejte příslušné hodnoty pro pole ověření identity.

Ověření identity čipem TPM:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Ověření identity X. 509:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Ověření identity symetrického klíče:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Vložení obsahu schránky do nano `Shift+Right Click` nebo stisknutím klávesy `Shift+Insert` .

Uložte soubor a zavřete ho. `CTRL + X`, `Y`, `Enter`

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

Spusťte [Nástroj pro řešení potíží](troubleshoot.md#run-the-check-command) , ve kterém zjistíte nejběžnější chyby konfigurace a sítě:

```bash
sudo iotedge check
```

Až nasadíte svůj první modul, abyste IoT Edgei na zařízení, modul **$edgeHub** systému nebude na zařízení nasazený. V důsledku toho automatizovaná kontroly vrátí chybu pro `Edge Hub can bind to ports on host` kontrolu připojení. Tuto chybu je možné ignorovat, pokud se neobjeví po nasazení modulu do zařízení.

Nakonec seznam spuštěných modulů:

```bash
sudo iotedge list
```

Jediným modulem, který by měl být spuštěný, je po instalaci IoT Edge na zařízení **edgeAgent**. Po vytvoření prvního nasazení se v zařízení spustí i jiný systémový modul **$edgeHub** . Další informace najdete v tématu [nasazení IoT Edgech modulů](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Tipy a řešení potíží

Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Po instalaci modulu runtime se odhlaste z počítače a znovu se přihlaste, abyste mohli aktualizovat vaše oprávnění automaticky. Až pak použijte **sudo** před všemi `iotedge` příkazy.

U zařízení s omezeným prostředkem se důrazně doporučuje nastavit proměnnou prostředí *OptimizeForPerformance* na *hodnotu false* podle pokynů v [Průvodci odstraňováním potíží](troubleshoot.md).

Pokud se vaše zařízení nemůže připojit k IoT Hub a vaše síť má proxy server, postupujte podle pokynů v části [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Ověření kompatibility se systémem Linux v jádře Moby

Řada integrovaných výrobců zařízení dodává obrázky zařízení, které obsahují vlastní jádra systému Linux bez funkcí požadovaných pro kompatibilitu s modulem runtime kontejneru. Pokud narazíte na problémy při instalaci doporučeného modulu runtime Moby Container, možná budete moci vyřešit potíže s konfigurací jádra pro Linux pomocí skriptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) z oficiálního [úložiště GitHub Moby](https://github.com/moby/moby). Spuštěním následujících příkazů na zařízení ověřte konfiguraci jádra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Tento příkaz poskytuje podrobný výstup, který obsahuje stav funkcí jádra používaných modulem runtime Moby. Budete chtít zajistit, aby všechny položky v `Generally Necessary` a  `Network Drivers` byly povoleny, aby bylo zajištěno, že je jádro plně kompatibilní s modulem runtime Moby.  Pokud jste identifikovali chybějící funkce, povolte je opětovným sestavením jádra ze zdroje a výběrem přidružených modulů pro zahrnutí do příslušného souboru kernel. config.  Podobně platí, že pokud používáte generátor konfigurace jádra, jako `defconfig` `menuconfig` je nebo, najděte a povolte příslušné funkce a odpovídajícím způsobem Sestavte jádro.  Po nasazení nově upraveného jádra spusťte skript check-config znovu, abyste ověřili, že všechny požadované funkce byly úspěšně povoleny.

## <a name="install-runtime-using-release-assets"></a>Instalace modulu runtime pomocí prostředků vydaných verzí

Postup v této části použijte, pokud chcete nainstalovat určitou verzi Azure IoT Edge runtime, která není dostupná prostřednictvím `apt-get install` . Seznam balíčků Microsoft obsahuje jenom omezené sady posledních verzí a jejich dílčích verzí, takže tento postup je pro kohokoli, kdo chce nainstalovat starší verzi nebo Release Candidate verzi.

Pomocí příkazů oblé můžete cílit na soubory součásti přímo z úložiště GitHub IoT Edge. K instalaci libiothsm a procesu démona zabezpečení IoT Edge použijte následující postup.

1. Připravte zařízení s nainstalovaným kontejnerovým modulem. Pokud nemáte modul kontejnerů, postupujte podle pokynů k registraci úložiště Microsoft a instalaci Moby do části [Instalace modulu runtime kontejneru a IoT Edge](#install-container-runtime-and-iot-edge) v tomto článku.

2. Přejděte do vydaných verzí [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)a vyhledejte verzi pro vydání, na kterou chcete cílit.

3. Rozbalte část **assets (prostředky** ) pro danou verzi.

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

Po úspěšné instalaci IoT Edge na `/etc/iotedge` výstupu se zobrazí výzva k aktualizaci konfiguračního souboru. Dokončete zřizování zařízení podle kroků v části [Konfigurace démona zabezpečení](#configure-the-security-daemon) .

## <a name="uninstall-iot-edge"></a>Odinstalace IoT Edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení se systémem Linux, použijte následující příkazy z příkazového řádku.

Odeberte modul runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Po odebrání IoT Edge modulu runtime dojde k zastavení kontejnerů, které vytvořili, ale stále existují na vašem zařízení. Zobrazením všech kontejnerů zjistíte, které z nich zůstanou.

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
