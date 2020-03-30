---
title: Instalace Azure IoT Edge na Linux | Dokumenty společnosti Microsoft
description: Pokyny k instalaci Azure IoT Edge na zařízeních s Linuxem s Ubuntu nebo Raspbianem
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535897"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Instalace modulu runtime Azure IoT Edge v systémech Linux založených na distribuci Debian

Azure IoT Edge runtime je to, co změní zařízení na zařízení IoT Edge. Runtime lze nasadit na zařízení tak malá jako Raspberry Pi nebo velká jako průmyslový server. Jakmile je zařízení nakonfigurované s runtime IoT Edge, můžete do něj začít nasazovat obchodní logiku z cloudu. Další informace najdete [v tématu Principy azure iot edge runtime a jeho architektura](iot-edge-runtime.md).

Tento článek uvádí kroky k instalaci runtime Azure IoT Edge na zařízení X64, ARM32 nebo ARM64 Linux. Poskytujeme instalační balíčky pro Ubuntu Server 16.04, Ubuntu Server 18.04 a Raspbian Stretch. Seznam podporovaných operačních systémů a architektur Linuxu najdete v [podporovaných systémech Azure IoT Edge.](support.md#operating-systems)

> [!NOTE]
> Balíčky v úložištích linuxového softwaru podléhají licenčním podmínkám umístěným v*package-name*každém balíčku (/usr/share/doc/package-name ). Před použitím balíčku si přečtěte licenční podmínky. Vaše instalace a používání balíčku představuje váš souhlas s těmito podmínkami. Pokud nesouhlasíte s licenčními podmínkami, balíček nepoužívejte.

## <a name="install-the-latest-runtime-version"></a>Instalace nejnovější verze runtime

Pomocí následujících částí nainstalujte do zařízení nejnovější verzi runtime Azure IoT Edge.

>[!NOTE]
>Podpora pro zařízení ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrace zdroje klíčů a úložiště softwaru Microsoft

Připravte zařízení na runtime instalaci IoT Edge.

Nainstalujte konfiguraci úložiště. Zvolte příkaz **16.04** nebo **18.04,** který odpovídá operačnímu systému vašeho zařízení:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

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

Instalace veřejného klíče Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Instalace běhu kontejneru

Azure IoT Edge spoléhá na runtime kontejneru [kompatibilní s OCI.](https://www.opencontainers.org/) Pro produkční scénáře doporučujeme použít modul [založený na Moby,](https://mobyproject.org/) který je k dispozici níže. Motor Moby je jediný modul kontejneru, který je oficiálně podporován azure iot edge. Image kontejneru Docker CE/EE jsou kompatibilní s moby runtime.

Aktualizujte seznamy balíčků v zařízení.

   ```bash
   sudo apt-get update
   ```

Nainstalujte motor Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Nainstalujte rozhraní příkazového řádku Moby (CLI). ClI je užitečné pro vývoj, ale volitelné pro nasazení v produkčním prostředí.

   ```bash
   sudo apt-get install moby-cli
   ```

Pokud se při instalaci modulu runtime kontejneru Moby zobrazí chyby, postupujte podle pokynů k [ověření linuxového jádra pro kompatibilitu Moby](#verify-your-linux-kernel-for-moby-compatibility), které jsou uvedeny dále v tomto článku.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalace daemonu zabezpečení Azure IoT Edge

**Déon zabezpečení IoT Edge** poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Daemon začíná při každém spuštění a zavádí zařízení spuštěním zbytku runtime IoT Edge.

Instalační příkaz také nainstaluje standardní verzi **libiothsmu,** pokud již není k dispozici.

Aktualizujte seznamy balíčků v zařízení.

   ```bash
   sudo apt-get update
   ```

Nainstalujte bezpečnostního daemona. Balíček je `/etc/iotedge/`nainstalován na adrese .

   ```bash
   sudo apt-get install iotedge
   ```

Po úspěšné instalaci ioT Edge vás výstup vyzve k aktualizaci konfiguračního souboru. Postupujte podle pokynů v části [Konfigurace demonu zabezpečení](#configure-the-security-daemon) k dokončení zřizování zařízení.

## <a name="install-a-specific-runtime-version"></a>Instalace konkrétní verze runtime

Pokud chcete nainstalovat konkrétní verzi Moby a Azure IoT Edge runtime namísto použití nejnovějších verzí, můžete cílit na soubory komponent přímo z úložiště IoT Edge GitHub. Pomocí následujících kroků přenesete do zařízení všechny součásti IoT Edge: motor Moby a cli, libiothsm a nakonec dém zabezpečení IoT Edge. Pokud nechcete změnit na konkrétní verzi runtime, přejděte k další části [Konfigurace daemonu zabezpečení.](#configure-the-security-daemon)

1. Přejděte na [verze Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)a najděte verzi, na kterou chcete cílit.

2. Rozbalte část **Prostředky** pro tuto verzi.

3. V dané verzi může nebo nemusí být aktualizace motoru Moby. Pokud se zobrazí soubory začínající **moby-engine** a **moby-cli**, použijte následující příkazy k aktualizaci těchto součástí. Pokud nevidíte žádné moby soubory, procházet starší verze datových zdrojů, dokud nenajdete nejnovější verzi.

   1. Najděte soubor **moby-engine,** který odpovídá architektuře zařízení IoT Edge. Klikněte pravým tlačítkem myši na odkaz na soubor a zkopírujte adresu odkazu.

   2. K instalaci této verze motoru Moby použijte zkopírovaný odkaz v následujícím příkazu:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Najděte soubor **moby-cli,** který odpovídá architektuře zařízení IoT Edge. Moby CLI je volitelná součást, ale může být užitečné během vývoje. Klikněte pravým tlačítkem myši na odkaz na soubor a zkopírujte adresu odkazu.

   4. K instalaci této verze příkazového příkazu Moby použijte zkopírovaný odkaz v následujícím příkazu:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Každé vydání by mělo mít nové soubory pro daemon zabezpečení IoT Edge a hsmlib. K aktualizaci těchto součástí použijte následující příkazy.

   1. Najděte soubor **libiothsm-std,** který odpovídá architektuře zařízení IoT Edge. Klikněte pravým tlačítkem myši na odkaz na soubor a zkopírujte adresu odkazu.

   2. K instalaci této verze hsmlib použijte zkopírovaný odkaz v následujícím příkazu:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Najděte soubor **iotedge,** který odpovídá architektuře zařízení IoT Edge. Klikněte pravým tlačítkem myši na odkaz na soubor a zkopírujte adresu odkazu.

   4. Pomocí zkopírovaného odkazu v následujícím příkazu nainstalujte tuto verzi daemonu zabezpečení IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Po úspěšné instalaci ioT Edge vás výstup vyzve k aktualizaci konfiguračního souboru. Postupujte podle kroků v další části k dokončení zřizování zařízení.

## <a name="configure-the-security-daemon"></a>Konfigurace daemonu zabezpečení

Nakonfigurujte runtime IoT Edge tak, aby propojil vaše fyzické zařízení s identitou zařízení, která existuje v centru Azure IoT.

Daemon lze nakonfigurovat pomocí konfiguračního souboru na adrese `/etc/iotedge/config.yaml`. Soubor je ve výchozím nastavení chráněn proti zápisu, k jeho úpravám může být pravděpodobně nutné jeho úpravy.

Jedno zařízení IoT Edge lze zřídit ručně pomocí řetězce připojení zařízení poskytovaného službou IoT Hub. Nebo můžete použít službu zřizování zařízení k automatickému zřizování zařízení, což je užitečné, když máte mnoho zařízení k zřízení. V závislosti na volbě zřizování zvolte příslušný instalační skript.

### <a name="option-1-manual-provisioning"></a>Možnost 1: Ruční zřizování

Chcete-li zařízení zřídit ručně, musíte mu poskytnout [připojovací řetězec zařízení,](how-to-register-device.md#register-in-the-azure-portal) který můžete vytvořit registrací nového zařízení ve vašem centru IoT Hub.

Otevřete konfigurační soubor.

```bash
sudo nano /etc/iotedge/config.yaml
```

Najděte konfigurace zřizování souboru a odkomentujte část **Ruční konfigurace zřizování.** Aktualizujte hodnotu **device_connection_string** pomocí připojovacího řetězce ze zařízení IoT Edge. Ujistěte se, že všechny ostatní zřizování oddíly jsou komentované. Ujistěte se, že **zřizování:** řádek nemá žádné předchozí mezery a že vnořené položky jsou odsazeny dvěma mezerami.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Chcete-li vložit obsah `Shift+Right Click` schránky `Shift+Insert`do nano nebo stiskněte tlačítko .

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Možnost 2: Automatické zřizování

Zařízení IoT Edge se dá automaticky zřídit pomocí [služby Azure IoT Hub Device Provisioning Service (DPS).](../iot-dps/index.yml) V současné době IoT Edge podporuje dva mechanismy ověřování při použití automatickézřižení, ale vaše požadavky na hardware může mít vliv na vaše volby. Například zařízení Raspberry Pi nejsou ve výchozím nastavení dodávána s čipem TPM (Trusted Platform Module). Další informace najdete v těchto článcích:

* [Vytvoření a zřízení zařízení IoT Edge s virtuálním čipem TPM na virtuálním počítači s Linuxem](how-to-auto-provision-simulated-device-linux.md)
* [Vytvoření a zřízení zařízení IoT Edge pomocí certifikátů X.509](how-to-auto-provision-x509-certs.md)
* [Vytvoření a zřízení zařízení IoT Edge pomocí symetrického atestace klíče](how-to-auto-provision-symmetric-keys.md)

Tyto články vás provedou nastavením zápisů v DPS a generováním správných certifikátů nebo klíčů pro ověřování. Bez ohledu na to, který mechanismus ověřování zvolíte, informace o zřizování se přidá do konfiguračního souboru IoT Edge na vašem zařízení IoT Edge.

Otevřete konfigurační soubor.

```bash
sudo nano /etc/iotedge/config.yaml
```

Najděte konfigurace zřizování souboru a odkomentujte oddíl vhodný pro mechanismus ověřování. Ujistěte se, že všechny ostatní zřizování oddíly jsou komentované. **Zřizování:** řádek by neměl mít žádné předchozí mezery a vnořené položky by měly být odsazeny dvěma mezerami. Aktualizujte hodnotu **scope_id** hodnotou z instance služby Zřizování zařízení služby IoT Hub a zadejte příslušné hodnoty pro pole atestace.

Osvědčení tpm:

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

X.509 osvědčení:

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

Symetrické atestace klíče:

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

Chcete-li vložit obsah `Shift+Right Click` schránky `Shift+Insert`do nano nebo stiskněte tlačítko .

Uložte soubor a zavřete ho. `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud jste použili **ruční kroky konfigurace** v předchozí části, runtime IoT Edge by měl být úspěšně zřízena a běží na vašem zařízení. Pokud jste **použili** kroky automatické konfigurace, pak je třeba provést některé další kroky, aby runtime můžete zaregistrovat vaše zařízení s centrem IoT vaším jménem. Další kroky najdete v [tématu Vytvoření a zřízení simulovaného zařízení TPM IoT Edge na virtuálním počítači s Linuxem](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Můžete zkontrolovat stav daemonu IoT Edge:

```bash
systemctl status iotedge
```

Zkontrolujte protokoly daemonů:

```bash
journalctl -u iotedge --no-pager --no-full
```

Spusťte automatickou kontrolu nejčastějších chyb konfigurace a sítě:

```bash
sudo iotedge check
```

Dokud nenasadíte první modul do ioT Edge na vašem zařízení, **$edgeHub** systémový modul se do zařízení nenasadí. V důsledku toho automatická kontrola vrátí chybu `Edge Hub can bind to ports on host` pro kontrolu připojení. Tato chyba může být ignorována, pokud k ní dojde po nasazení modulu do zařízení.

Nakonec seznam spuštěných modulů:

```bash
sudo iotedge list
```

Po instalaci IoT Edge na vašem zařízení, jediný modul, který byste měli vidět běží je **edgeAgent**. Po vytvoření prvního nasazení se v zařízení spustí také další modul systému **$edgeHub.** Další informace naleznete v [tématu nasazení modulů IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Tipy a řešení potíží

Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Po instalaci runtime se odhlaste od počítače a znovu se přihlaste a automaticky aktualizujte svá oprávnění. Do té doby, použijte **sudo** před všemi `iotedge` příkazy.

Na zařízeních s omezenými prostředky důrazně doporučujeme nastavit proměnnou prostředí *OptimizeForPerformance* na *hodnotu false* podle pokynů v [průvodci odstraňováním potíží](troubleshoot.md).

Pokud vaše síť, která má proxy server, postupujte podle pokynů v [části Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy serveru](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Ověření kompatibility linuxového jádra moby

Mnoho výrobců vestavěných zařízení doručuje imitace zařízení, které obsahují vlastní jádra Linuxu bez funkcí požadovaných pro kompatibilitu kontejnerového modulu runtime. Pokud při instalaci doporučeného modulu runtime kontejneru Moby narazíte na problémy, můžete vyřešit potíže s konfigurací jádra Linuxu pomocí skriptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) z oficiálního [úložiště Moby GitHub](https://github.com/moby/moby). Spusťte v zařízení následující příkazy a zkontrolujte konfiguraci jádra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Tento příkaz poskytuje podrobný výstup, který obsahuje stav funkcí jádra, které jsou používány modulem Runtime Moby. Budete chtít zajistit, aby `Generally Necessary` všechny `Network Drivers` položky pod a jsou povoleny, aby zajistily, že vaše jádro je plně kompatibilní s runtime Moby.  Pokud jste identifikovali chybějící funkce, povolte je opětovným sestavením jádra ze zdroje a výběrem přidružených modulů pro zařazení do příslušného jádra .config.  Podobně, pokud používáte generátor konfigurace `defconfig` jádra, jako je nebo `menuconfig`, najít a povolit příslušné funkce a obnovit jádro odpovídajícím způsobem.  Po nasazení nově upraveného jádra znovu spusťte skript check-config a ověřte, zda byly úspěšně povoleny všechny požadované funkce.

## <a name="uninstall-iot-edge"></a>Odinstalace ioT edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení s Linuxem, použijte následující příkazy z příkazového řádku.

Odeberte modul runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Po odebrání runtime IoT Edge kontejnery, které vytvořil, jsou zastaveny, ale stále existují na vašem zařízení. Zobrazit všechny kontejnery vidět, které z nich zůstávají.

```bash
sudo docker ps -a
```

Odstraňte kontejnery ze zařízení, včetně dvou kontejnerů runtime.

```bash
sudo docker rm -f <container name>
```

Nakonec odeberte za běhu kontejneru ze zařízení.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge zřízené s nainstalovaným modulem runtime, můžete [nasadit moduly IoT Edge](how-to-deploy-modules-portal.md).

Pokud máte problémy s instalací runtime IoT Edge správně, podívejte se na stránku [řešení potíží.](troubleshoot.md)

Informace o aktualizaci existující instalace na nejnovější verzi technologie IoT Edge naleznete v [tématu Aktualizace daemonu zabezpečení IoT Edge a runtime](how-to-update-iot-edge.md).
