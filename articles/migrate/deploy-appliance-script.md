---
title: Nastavení zařízení Azure Migrate pomocí skriptu
description: Naučte se, jak nastavit zařízení Azure Migrate pomocí skriptu.
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714753"
---
# <a name="set-up-an-appliance-with-a-script"></a>Nastavení zařízení pomocí skriptu

Podle tohoto článku vytvořte [zařízení Azure Migrate](./migrate-appliance-architecture.md) pro účely posouzení/migrace serverů na VMware a Hyper-V. Spustíte skript pro vytvoření zařízení a ověříte, že se může připojit k Azure. 

Zařízení můžete nasadit pro servery na VMware a Hyper-V pomocí skriptu nebo pomocí šablony, kterou stáhnete z Azure Portal. Použití skriptu je užitečné v případě, že nemůžete vytvořit zařízení pomocí stažené šablony.

- Chcete-li použít šablonu, postupujte podle kurzů pro [VMware](./tutorial-discover-vmware.md) nebo [Hyper-V](./tutorial-discover-hyper-v.md).
- K nastavení zařízení pro fyzické servery můžete použít jenom skript. Postupujte podle [tohoto článku](how-to-set-up-appliance-physical.md).
- Pokud chcete zařízení nastavit v cloudu Azure Government, postupujte podle pokynů v [tomto článku](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Požadavky

Skript nastaví zařízení Azure Migrate na existujícím serveru.

- Server, který bude fungovat jako zařízení, musí splňovat následující požadavky na hardware a operační systém:

Scenario | Požadavky
--- | ---
VMware | Windows Server 2016 s 32 GB paměti, 8 vCPU, kolem 80 GB diskového úložiště
Hyper-V | Windows Server 2016, 16 GB paměti, 8 vCPU, přibližně 80 GB diskového úložiště

- Server taky potřebuje externí virtuální přepínač. Vyžaduje statickou nebo dynamickou IP adresu. 
- Před nasazením zařízení si přečtěte podrobný požadavky na zařízení pro [servery v VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Nespouštějte skript na existujícím zařízení Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Nastavení zařízení pro VMware

Pokud chcete nastavit zařízení pro VMware, Stáhněte si soubor zip s názvem AzureMigrateInstaller-Server-Public.zip buď z portálu, nebo z [tohoto místa](https://go.microsoft.com/fwlink/?linkid=2140334)a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu.

### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na serveru, na který jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Ověřte verzi nejnovějšího a skriptu pro veřejný cloud Azure:

    **Algoritmus** | **Stáhnout** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>Spuštění skriptu

Tento skript:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje modul IIS s možností zápisu. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Azure Migrate.
- Vytvoří protokol a konfigurační soubory následujícím způsobem:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spuštění skriptu:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení. Ujistěte se, že jste skript nespouštěli na stávajícím zařízení Azure Migrate.
2. Na serveru spusťte PowerShell s oprávněními správce (zvýšené oprávnění).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1** následujícím způsobem:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="set-up-the-appliance-for-hyper-v"></a>Nastavení zařízení pro Hyper-V

Pokud chcete nastavit zařízení pro Hyper-V, Stáhněte si soubor zip s názvem AzureMigrateInstaller-Server-Public.zip buď z portálu, nebo z [tohoto místa](https://go.microsoft.com/fwlink/?linkid=2105112)a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu.


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na serveru, na který jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Ověřte verzi nejnovějšího a skriptu pro veřejný cloud Azure:

    **Scénář** | **Stáhnout** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>Spuštění skriptu

Tento skript:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje modul IIS s možností zápisu. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Azure Migrate.
- Vytvoří protokol a konfigurační soubory následujícím způsobem:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spuštění skriptu:

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení. Ujistěte se, že jste skript nespouštěli na stávajícím zařízení Azure Migrate.
2. Na serveru spusťte PowerShell s oprávněními správce (zvýšené oprávnění).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1** následujícím způsobem:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="next-steps"></a>Další kroky

Po nasazení zařízení je třeba ho nakonfigurovat poprvé a zaregistrovat ho v Projectu.

- Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Nastavte zařízení pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).