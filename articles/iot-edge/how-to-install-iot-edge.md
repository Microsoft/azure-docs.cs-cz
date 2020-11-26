---
title: Nainstalovat Azure IoT Edge | Microsoft Docs
description: Pokyny k instalaci Azure IoT Edge na zařízeních se systémem Windows nebo Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: a7794bcdfa4f82698fdc5875bc94dcf52b70166e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185092"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Instalace nebo odinstalace modulu runtime Azure IoT Edge

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

Existují dva kroky pro nastavení IoT Edge zařízení. Prvním krokem je instalace modulu runtime a jeho závislostí, který je popsaný v tomto článku. Druhým krokem je připojit zařízení k jeho identitě v cloudu a nastavit ověřování pomocí IoT Hub. Tyto kroky jsou uvedené v dalších článcích.

V tomto článku jsou uvedené kroky pro instalaci modulu runtime Azure IoT Edge v zařízeních se systémem Linux nebo Windows. U zařízení s Windows máte k dispozici další možnosti použití kontejnerů systému Linux nebo kontejnerů Windows. V současné době jsou kontejnery Windows ve Windows doporučovány pro produkční scénáře. Kontejnery pro Linux ve Windows jsou užitečné pro scénáře vývoje a testování, zejména pokud vyvíjíte na počítači s Windows, abyste mohli nasadit na zařízení se systémem Linux.

## <a name="prerequisites"></a>Předpoklady

Nejnovější informace o tom, které operační systémy se v produkčních scénářích aktuálně podporují, najdete v článku [Azure IoT Edge podporované systémy](support.md#operating-systems) .

# <a name="linux"></a>[Linux](#tab/linux)

Mít zařízení s platformou x64, ARM32 nebo ARM64 Linux. Microsoft poskytuje instalační balíčky pro Ubuntu Server 16,04, Ubuntu Server 18,04 a Raspbian Stretch operačních systémů.

>[!NOTE]
>Podpora pro zařízení ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Připravte zařízení pro přístup k instalačním balíčkům společnosti Microsoft.

1. Nainstalujte konfiguraci úložiště, která odpovídá operačnímu systému vašeho zařízení.

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

2. Zkopírujte vygenerovaný seznam do adresáře sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Nainstalujte veřejný klíč Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Verze systému Windows

IoT Edge s kontejnery Windows vyžaduje systém Windows verze 1809/Build 17762, což je nejnovější [sestavení s podporou dlouhodobé podpory Windows](/windows/release-information/). Pro vývojové a testovací scénáře budou fungovat všechny SKU (pro, Enterprise, server atd.), které podporují funkci Containers. Před vstupem do produkčního prostředí však nezapomeňte seznam podporovaných systémů zkontrolovat.

IoT Edge s kontejnery pro Linux lze spustit v libovolné verzi systému Windows, která splňuje [požadavky pro Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Požadavky na modul kontejneru

Azure IoT Edge spoléhá na modul kontejneru, který je kompatibilní s rozhraním [OCI](https://www.opencontainers.org/) . Ujistěte se, že vaše zařízení dokáže podporovat kontejnery.

Pokud instalujete IoT Edge na virtuálním počítači, povolte vnořenou virtualizaci a přidělte alespoň 2 GB paměti. Virtuální počítače generace 2 pro Hyper-V mají ve výchozím nastavení vnořenou virtualizaci povolenou. Pro VMware je k dispozici přepínač pro povolení funkce na vašem virtuálním počítači.

Pokud instalujete IoT Edge na zařízení IoT Core, pomocí následujícího příkazu ve [vzdálené relaci PowerShellu](/windows/iot-core/connect-your-device/powershell) ověřte, jestli jsou kontejnery Windows na vašem zařízení podporované:

```powershell
Get-Service vmcompute
```

---

Azure IoT Edge softwarové balíčky podléhají licenčním podmínkám, které jsou umístěny v každém balíčku ( `usr/share/doc/{package-name}` nebo `LICENSE` adresáři). Přečtěte si licenčních podmínek před použitím balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte tento balíček.

## <a name="install-a-container-engine"></a>Instalace modulu kontejneru

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním OCI. V produkčních scénářích doporučujeme použít modul založený na Moby. Modul Moby je jediným kontejnerovým modulem, který je oficiálně podporován s Azure IoT Edge. Image kontejnerů Docker CE/EE jsou kompatibilní s modulem runtime Moby.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

V produkčních scénářích použijte modul založený na Moby, který je součástí instalačního skriptu. Není k dispozici žádný další postup pro instalaci modulu.

Pro IoT Edge s kontejnery pro Linux je potřeba poskytnout vlastní modul runtime kontejneru. Než budete pokračovat, nainstalujte na zařízení [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) a nakonfigurujte ho tak, aby [používal kontejnery Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .

---

## <a name="install-the-iot-edge-security-daemon"></a>Instalace démona zabezpečení IoT Edge

Démon zabezpečení IoT Edge poskytuje a udržuje standardy zabezpečení na zařízení IoT Edge. Démon procesu se spustí při každém spuštění a spustí zařízení spuštěním zbytku modulu runtime IoT Edge.

Postup v této části představuje typický proces instalace nejnovější verze do zařízení, které má připojení k Internetu. Pokud potřebujete nainstalovat určitou verzi, třeba předběžnou verzi, nebo potřebujete instalaci v režimu offline, postupujte podle pokynů k [instalaci offline nebo specifické verze](#offline-or-specific-version-installation) v následující části.

# <a name="linux"></a>[Linux](#tab/linux)

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

Pokud chcete nainstalovat určitou verzi démona zabezpečení, zadejte verzi z výstupu seznamu apt. Zadejte také stejnou verzi balíčku **libiothsm-STD** , která by jinak instalovala nejnovější verzi. Například následující příkaz nainstaluje nejnovější verzi 1.0.8 verze:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Pokud verze, kterou chcete nainstalovat, není v seznamu uvedená, postupujte podle pokynů k [instalaci offline nebo specifické verze](#offline-or-specific-version-installation) v následující části. V této části se dozvíte, jak cílit na předchozí verzi IoT Edge démona zabezpečení nebo Release Candidate verze.

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>Pro zařízení IoT Core doporučujeme spouštět instalační příkazy pomocí vzdálené relace PowerShellu. Další informace najdete v tématu [použití PowerShellu pro Windows IoT](/windows/iot-core/connect-your-device/powershell).

1. Spusťte PowerShell jako správce.

   Použijte relaci AMD64 PowerShellu, ne PowerShell (x86). Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Spusťte příkaz [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , který provede následující úlohy:

   * Kontroluje, zda má počítač s Windows podporovanou verzi.
   * Zapne funkci Containers.
   * Stáhne modul Moby a modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`Příkaz ve výchozím nastavení používá kontejnery Windows. Pokud chcete použít kontejnery pro Linux, přidejte `ContainerOs` parametr:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní.

Při instalaci IoT Edge na zařízení můžete pomocí dalších parametrů upravit proces, včetně:

* Směrování provozu pomocí proxy server
* Najeďte instalačnímu programu na místní adresář pro instalaci offline.

Další informace o těchto dalších parametrech najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

---

Teď, když je v zařízení nainstalovaný modul pro vytváření kontejnerů a IoT Edge runtime, jste připraveni k dalšímu kroku, který zaregistruje zařízení v IoT Hub a nastaví zařízení s ověřovacími informacemi a identitami v cloudu.

V závislosti na typu ověřování, který chcete použít, vyberte další článek:

* [Ověřování symetrického klíče](how-to-manual-provision-symmetric-key.md) je rychlejší, aby bylo možné začít.
* [Ověřování certifikátu X. 509](how-to-manual-provision-x509.md) je bezpečnější pro produkční scénáře.

## <a name="offline-or-specific-version-installation"></a>Instalace offline nebo specifické verze

Kroky v této části se týkají scénářů, které nejsou zahrnuté do standardních kroků instalace. To může zahrnovat:

* Nainstalovat IoT Edge v režimu offline
* Nainstalovat Release Candidate verzi
* V systému Windows nainstalujte jinou verzi než nejnovější.

# <a name="linux"></a>[Linux](#tab/linux)

Postup v této části použijte, pokud chcete nainstalovat určitou verzi Azure IoT Edge runtime, která není dostupná prostřednictvím `apt-get install` . Seznam balíčků Microsoft obsahuje jenom omezené sady posledních verzí a jejich dílčích verzí, takže tento postup je pro kohokoli, kdo chce nainstalovat starší verzi nebo Release Candidate verzi.

Pomocí příkazů oblé můžete cílit na soubory součásti přímo z úložiště GitHub IoT Edge.

<!-- TODO: Offline installation? -->

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

# <a name="windows"></a>[Windows](#tab/windows)

Během instalace se stáhnou tři soubory:

* Skript prostředí PowerShell, který obsahuje pokyny k instalaci
* Microsoft Azure IoT Edge soubor CAB obsahující rozhraní Moby (IoT Edge Security Daemon), modul kontejnerů Moby a CLI
* Instalační program pro Visual C++ Distribuovatelný balíček (VC Runtime)

Pokud bude vaše zařízení během instalace offline nebo pokud chcete nainstalovat určitou verzi IoT Edge, můžete tyto soubory stáhnout na zařízení předem. Až bude čas na instalaci, najeďte instalační skript v adresáři, který obsahuje stažené soubory. Instalační program nejprve zkontroluje adresář a pak stáhne pouze součásti, které nebyly nalezeny. Pokud jsou všechny soubory k dispozici v režimu offline, můžete nainstalovat bez připojení k Internetu.

1. Nejnovější instalační soubory IoT Edge spolu s předchozími verzemi najdete v tématu [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

2. Vyhledejte verzi, kterou chcete nainstalovat, a Stáhněte si následující soubory z části **assets (prostředky** ) poznámky k verzi do zařízení IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z verzí 1.0.9 nebo novější, nebo Microsoft-Azure-IoTEdge.cab z verzí 1.0.8 a starších.

   Microsoft-Azure-IotEdge-arm32.cab je k dispozici také pro účely testování, od 1.0.9. IoT Edge se v současné době nepodporují na zařízeních s Windows ARM32.

   Je důležité použít skript prostředí PowerShell ze stejné verze, jako je soubor. cab, který používáte, protože funkce se mění tak, aby podporovaly funkce v jednotlivých vydaných verzích.

3. Pokud má soubor. cab, který jste stáhli, příponu architektury, přejmenujte soubor na pouze **Microsoft-Azure-IoTEdge.cab**.

4. Volitelně můžete stáhnout instalační program pro Visual C++ Redistributable. Například skript PowerShellu používá tuto verzi: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Uložte instalační program do stejné složky v zařízení IoT jako soubory IoT Edge.

5. Chcete [-li nainstalovat](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) nástroje s offline komponentami, poznamenejte si místní kopii skriptu PowerShellu. 

6. Spusťte příkaz [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) s `-OfflineInstallationPath` parametrem. Zadejte absolutní cestu k adresáři souborů. Třeba

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Příkaz pro nasazení bude používat všechny součásti, které jsou k dispozici v adresáři místního souboru. Pokud chybí soubor. cab nebo instalační program Visual C++, pokusí se ho stáhnout.

---

Teď, když je v zařízení nainstalovaný modul kontejneru a modul runtime IoT Edge, jste připraveni na další krok, který [v IoT Hub ověřuje IoT Edge zařízení](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>Odinstalace IoT Edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení, použijte následující příkazy.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

Pokud chcete odebrat instalaci IoT Edge ze zařízení s Windows, použijte příkaz [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) z okna pro správu prostředí PowerShell. Tento příkaz odebere modul runtime IoT Edge společně se stávající konfigurací a daty modulu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge`Příkaz nefunguje na Windows IoT Core. Pokud chcete odebrat IoT Edge, musíte znovu nasadit image Windows IoT Core.

Další informace o možnostech odinstalace získáte pomocí příkazu `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Další kroky

Po instalaci modulu runtime IoT Edge nakonfigurujte své zařízení pro připojení k IoT Hub. V následujících článcích se dozvíte, jak zaregistrovat nové zařízení v cloudu a pak zařízení poskytnout jeho identitu a ověřovací informace.

V závislosti na typu ověřování, který chcete použít, vyberte další článek:

* **Symetrický klíč**: IoT Hub i IoT Edge zařízení mají kopii zabezpečeného klíče. Když se zařízení připojí k IoT Hub, zkontroluje, jestli se klíče shodují. Tato metoda ověřování je rychlejší, aby se dala začít, ale ne tak bezpečné.

  [Nastavení zařízení Azure IoT Edge s ověřováním pomocí symetrického klíče](how-to-manual-provision-symmetric-key.md)

* **X. 509 podepsaný svým držitelem**: zařízení IoT Edge má certifikáty X. 509 identity a IoT Hub se jim přihlásil kryptografický otisk certifikátů. Když se zařízení připojí k IoT Hub, porovná certifikát s jeho kryptografickým otiskem. Tato metoda ověřování je bezpečnější a doporučuje se pro produkční scénáře.

  [Nastavení zařízení Azure IoT Edge s ověřováním pomocí certifikátu X. 509](how-to-manual-provision-x509.md)