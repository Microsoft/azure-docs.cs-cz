---
title: Nainstalovat Azure IoT Edge | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na zařízeních se systémem Windows nebo Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
ms.openlocfilehash: ab783d6cb20f1c2fe31e8556dc57999df20d5637
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629806"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Instalace nebo odinstalace Azure IoT Edge pro Linux

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge v zařízeních se systémem Linux.

## <a name="prerequisites"></a>Požadavky

* [ID registrovaného zařízení](how-to-register-device.md)

  Pokud jste zařízení zaregistrovali pomocí ověřování symetrického klíče, připravte si připojovací řetězec zařízení.

  Pokud jste zařízení zaregistrovali pomocí ověřování certifikátu X. 509, musí mít alespoň jeden z certifikátů identity, které jste použili k registraci zařízení, a odpovídající privátní klíč, který je k dispozici na vašem zařízení.

* Zařízení se systémem Linux

  Mít zařízení s platformou x64, ARM32 nebo ARM64 Linux. Microsoft poskytuje instalační balíčky pro Ubuntu Server 16,04, Ubuntu Server 18,04 a maliny PI pro roztažené operační systémy.

  Nejnovější informace o tom, které operační systémy se v produkčních scénářích aktuálně podporují, najdete v článku [Azure IoT Edge podporované systémy](support.md#operating-systems) .

  >[!NOTE]
  >Podpora pro zařízení ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Připravte zařízení pro přístup k instalačním balíčkům společnosti Microsoft.

  Nainstalujte konfiguraci úložiště, která odpovídá operačnímu systému vašeho zařízení.

  * **Ubuntu Server 16,04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Ubuntu Server 18,04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Malina Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Zkopírujte vygenerovaný seznam do adresáře sources. list. d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Nainstalujte veřejný klíč Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge softwarové balíčky podléhají licenčním podmínkám, které jsou umístěny v každém balíčku ( `usr/share/doc/{package-name}` nebo `LICENSE` adresáři). Přečtěte si licenčních podmínek před použitím balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte tento balíček.

## <a name="install-a-container-engine"></a>Instalace modulu kontejneru

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním OCI. V produkčních scénářích doporučujeme použít modul Moby. Modul Moby je jediným kontejnerovým modulem, který je oficiálně podporován s Azure IoT Edge. Image kontejnerů Docker CE/EE jsou kompatibilní s modulem runtime Moby.

Aktualizuje seznamy balíčků na vašem zařízení.

   ```bash
   sudo apt-get update
   ```

Nainstalujte modul Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Pokud při instalaci modulu kontejneru Moby dojde k chybám, ověřte, že se v jádru Linux nachází kompatibilita Moby. Někteří výrobci integrovaných zařízení dodávají obrázky zařízení, které obsahují vlastní jádra systému Linux bez funkcí vyžadovaných pro kompatibilitu modulu kontejneru. Spusťte následující příkaz, který pomocí [skriptu check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) , který poskytuje Moby, zkontroluje konfiguraci jádra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Ve výstupu skriptu ověřte, zda jsou všechny položky pod `Generally Necessary` a `Network Drivers` povoleny. Pokud funkce postrádáte, povolte je opětovným sestavením jádra ze zdroje a výběrem přidružených modulů pro zahrnutí do příslušného jádra. config. Podobně platí, že pokud používáte generátor konfigurace jádra, jako `defconfig` `menuconfig` je nebo, najděte a povolte příslušné funkce a odpovídajícím způsobem Sestavte jádro. Po nasazení nově upraveného jádra spusťte skript check-config znovu, abyste ověřili, že všechny požadované funkce byly úspěšně povoleny.

## <a name="install-the-iot-edge-security-daemon"></a>Instalace démona zabezpečení IoT Edge

Démon zabezpečení IoT Edge poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Démon procesu se spustí při každém spuštění a spustí zařízení spuštěním zbytku modulu runtime IoT Edge.

Postup v této části představuje typický proces instalace nejnovější verze do zařízení, které má připojení k Internetu. Pokud potřebujete nainstalovat určitou verzi, třeba předběžnou verzi, nebo potřebujete instalaci v režimu offline, postupujte podle pokynů k [instalaci offline nebo specifické verze](#offline-or-specific-version-installation-optional) v následující části.

Aktualizuje seznamy balíčků na vašem zařízení.

   ```bash
   sudo apt-get update
   ```

Zkontrolujte, které verze IoT Edge jsou k dispozici.

   ```bash
   apt list -a iotedge
   ```

Chcete-li nainstalovat nejnovější verzi démona zabezpečení, použijte následující příkaz, který nainstaluje také nejnovější verzi balíčku **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Nebo pokud chcete nainstalovat určitou verzi démona zabezpečení, zadejte verzi z výstupu seznamu apt. Zadejte také stejnou verzi balíčku **libiothsm-STD** , která by jinak instalovala nejnovější verzi. Například následující příkaz nainstaluje nejnovější verzi 1.0.10 verze:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Pokud verze, kterou chcete nainstalovat, není v seznamu uvedená, postupujte podle pokynů k [instalaci offline nebo specifické verze](#offline-or-specific-version-installation-optional) dále v tomto článku. V této části se dozvíte, jak cílit na předchozí verzi IoT Edge démona zabezpečení nebo Release Candidate verze.

## <a name="provision-the-device-with-its-cloud-identity"></a>Zřízení zařízení pomocí cloudové identity

Teď, když je v zařízení nainstalovaný modul pro vytváření kontejnerů a IoT Edge runtime, jste připraveni k dalšímu kroku, ve kterém můžete nastavit zařízení s jeho identitou v cloudu a ověřovacími informacemi.

V závislosti na typu ověřování, který chcete použít, vyberte další oddíl:

* [Možnost 1: ověření pomocí symetrických klíčů](#option-1-authenticate-with-symmetric-keys)
* [Možnost 2: ověření pomocí certifikátů X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Možnost 1: ověření pomocí symetrických klíčů

V tomto okamžiku je IoT Edge modul runtime nainstalovaný na zařízení se systémem Linux a Vy musíte zařízení zřídit s jeho identitou v cloudu a ověřovacími informacemi.

Tato část vás provede postupem zřízení zařízení s ověřováním pomocí symetrického klíče. Zařízení byste měli zaregistrovali v IoT Hub a z informací o zařízení se načetl připojovací řetězec. V takovém případě postupujte podle pokynů v části [registrace zařízení IoT Edge v IoT Hub](how-to-register-device.md).

Na zařízení IoT Edge otevřete konfigurační soubor.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Vyhledejte konfigurace zřizování souboru a zrušte komentář k **ruční konfiguraci zřizování pomocí oddílu připojovacího řetězce** .

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

Aktualizujte hodnotu **device_connection_string** připojovacím řetězcem ze zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

Vložení obsahu schránky do nano `Shift+Right Click` nebo stisknutím klávesy `Shift+Insert` .

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="option-2-authenticate-with-x509-certificates"></a>Možnost 2: ověření pomocí certifikátů X. 509

V tomto okamžiku je IoT Edge modul runtime nainstalovaný na zařízení se systémem Linux a Vy musíte zařízení zřídit s jeho identitou v cloudu a ověřovacími informacemi.

V této části se seznámíte s postupem zřízení zařízení s ověřováním pomocí certifikátu X. 509. Měli byste mít zaregistrované zařízení v IoT Hub a poskytnou se kryptografické otisky, které odpovídají certifikátu a soukromému klíči, který se nachází v IoT Edgem zařízení. V takovém případě postupujte podle pokynů v části [registrace zařízení IoT Edge v IoT Hub](how-to-register-device.md).

Na zařízení IoT Edge otevřete konfigurační soubor.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

V souboru vyhledejte část konfigurace zřizování a odkomentujte **ruční konfiguraci zřizování pomocí oddílu certifikátu X. 509 identity** . Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. Ujistěte se, že **zřizování:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny dvěma mezerami.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

Aktualizujte následující pole:

* **iothub_hostname**: název hostitele IoT Hub, ke kterému se bude zařízení připojovat. Například `{IoT hub name}.azure-devices.net`.
* **device_ID**: ID, které jste zadali při registraci zařízení.
* **identity_cert**: identifikátor URI certifikátu identity na zařízení. Například `file:///path/identity_certificate.pem`.
* **identity_pk**: identifikátor URI pro soubor privátního klíče pro poskytnutý certifikát identity. Například `file:///path/identity_key.pem`.

Uložte soubor a zavřete ho.

   `CTRL + X`, `Y`, `Enter`

Po zadání informací o zřizování do konfiguračního souboru restartujte démona:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-successful-configuration"></a>Ověřit úspěšnou konfiguraci

Ověřte, že modul runtime byl úspěšně nainstalován a nakonfigurován na vašem zařízení IoT Edge.

1. Zkontrolujte, zda je démon zabezpečení IoT Edge spuštěn jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   >[!TIP]
   >Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Vaše oprávnění se automaticky aktualizují, jakmile se po instalaci modulu runtime IoT Edge odhlásíte z počítače a poprvé se k němu opět přihlásíte. Do té doby použijte `sudo` před příkazy.

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

   ```bash
   journalctl -u iotedge
   ```

3. Použijte `check` Nástroj k ověření stavu konfigurace a připojení zařízení.

   ```bash
   sudo iotedge check
   ```

   >[!TIP]
   >Vždy používejte `sudo` ke spuštění nástroje pro kontrolu i po aktualizaci vašich oprávnění. Tento nástroj potřebuje zvýšená oprávnění pro přístup k souboru **config. yaml** , aby bylo možné ověřit stav konfigurace.

4. Zobrazte všechny moduly spuštěné na vašem zařízení IoT Edge. Při prvním spuštění služby by se měl zobrazit jenom modul **edgeAgent** spuštěný. Ve výchozím nastavení se spustí modul edgeAgent a pomůže vám nainstalovat a spustit všechny další moduly, které nasadíte do svého zařízení.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Instalace offline nebo konkrétní verze (volitelné)

Kroky v této části se týkají scénářů, které nejsou zahrnuté do standardních kroků instalace. To může zahrnovat:

* Nainstalovat IoT Edge v režimu offline
* Nainstalovat Release Candidate verzi

Postup v této části použijte, pokud chcete nainstalovat určitou verzi Azure IoT Edge runtime, která není dostupná prostřednictvím `apt-get install` . Seznam balíčků Microsoft obsahuje jenom omezené sady posledních verzí a jejich dílčích verzí, takže tento postup je pro kohokoli, kdo chce nainstalovat starší verzi nebo Release Candidate verzi.

Pomocí příkazů oblé můžete cílit na soubory součásti přímo z úložiště GitHub IoT Edge.

1. Přejděte do vydaných verzí [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)a vyhledejte verzi pro vydání, na kterou chcete cílit.

2. Rozbalte část **assets (prostředky** ) pro danou verzi.

3. Každé vydání by mělo mít nové soubory pro démona zabezpečení IoT Edge a hsmlib. Tyto součásti můžete aktualizovat pomocí následujících příkazů.

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

Teď, když je v zařízení nainstalovaný modul pro vytváření kontejnerů a IoT Edge runtime, jste připraveni na další krok, kterým se [zařízení zřídí s cloudovou identitou](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Odinstalace IoT Edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení, použijte následující příkazy.

Odeberte modul runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Po odebrání modulu runtime IoT Edge dojde k zastavení všech kontejnerů, které byly vytvořeny, ale stále existují na vašem zařízení. Zobrazením všech kontejnerů zjistíte, které z nich zůstanou.

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

Pokračujte v [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) a Naučte se, jak nasadit moduly do svého zařízení.
