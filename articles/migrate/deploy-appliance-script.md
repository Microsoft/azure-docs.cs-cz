---
title: Nastavení zařízení Azure Migrate pomocí skriptu
description: Naučte se, jak nastavit zařízení Azure Migrate pomocí skriptu.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 20dbe4ba3b1b4858cb7022bb72129ee419ea2540
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331980"
---
# <a name="set-up-an-appliance-with-a-script"></a>Nastavení zařízení pomocí skriptu

Podle tohoto článku vytvořte [zařízení Azure Migrate](deploy-appliance.md) pro účely posouzení/migrace virtuálních počítačů VMware a virtuálních počítačů Hyper-V. Spustíte skript pro vytvoření zařízení a ověříte, že se může připojit k Azure. 

Zařízení pro virtuální počítače VMware a Hyper-V můžete nasadit pomocí skriptu nebo pomocí šablony, kterou stáhnete z Azure Portal. Použití skriptu je užitečné, pokud nemůžete vytvořit virtuální počítač pomocí stažené šablony.

- Chcete-li použít šablonu, postupujte podle kurzů pro [VMware](tutorial-prepare-vmware.md) nebo [Hyper-V](tutorial-prepare-hyper-v.md).
- K nastavení zařízení pro fyzické servery můžete použít jenom skript. Postupujte podle [tohoto článku](how-to-set-up-appliance-physical.md).
- Pokud chcete zařízení nastavit v cloudu Azure Government, postupujte podle pokynů v [tomto článku](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Požadavky

Skript nastaví zařízení Azure Migrate na stávajícím fyzickém počítači nebo VIRTUÁLNÍm počítači.

- Počítač, který bude fungovat jako zařízení, musí používat Windows Server 2016, 32 GB paměti, 8 vCPU, přibližně 80 GB diskového úložiště a externí virtuální přepínač. Vyžaduje statickou nebo dynamickou IP adresu a přístup k Internetu.
- Před nasazením zařízení si přečtěte podrobný požadavky na zařízení pro [virtuální počítače VMware](migrate-appliance.md#appliance---vmware), [virtuální počítače Hyper-V](migrate-appliance.md#appliance---hyper-v)a [fyzické servery](migrate-appliance.md#appliance---physical).
- Nespouštějte skript na existujícím zařízení Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Nastavení zařízení pro VMware

Pokud chcete nastavit zařízení pro VMware, Stáhněte si z Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu Azure Migrate.

### <a name="download-the-script"></a>Stáhnout skript

1.  V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2.  V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano s hypervisorem VMware vSphere**.
3.  Pro stažení souboru ZIP klikněte na **Stáhnout**. 


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Ověřte verzi nejnovějšího a skriptu pro veřejný cloud Azure:

    **Algoritmus** | **Stáhnout** | **SHA256**
    --- | --- | ---
    VMware (10,9 GB) | [Nejnovější verze](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd



### <a name="run-the-script"></a>Spuštění skriptu

Tento skript:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje modul IIS s možností zápisu. [Přečtěte si další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Azure Migrate.
- Vytvoří protokol a konfigurační soubory následujícím způsobem:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spuštění skriptu:

1. Extrahujte soubor zip do složky na počítači, který bude hostovat zařízení. Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na počítači spusťte PowerShell s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller. ps1**následujícím způsobem:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="set-up-the-appliance-for-hyper-v"></a>Nastavení zařízení pro Hyper-V

Pokud chcete nastavit zařízení pro Hyper-V, Stáhněte ze Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu Azure Migrate.

### <a name="download-the-script"></a>Stáhnout skript

1.  V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2.  V rozevíracích **seznamech počítačů**  >  , ve**kterých jsou počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3.  Pro stažení souboru ZIP klikněte na **Stáhnout**. 


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ověřte verzi nejnovějšího a skriptu pro veřejný cloud Azure:

    **Scénář** | **Stáhnout** | **SHA256**
    --- | --- | ---
    Hyper-V (8,93 MB) | [Nejnovější verze](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

### <a name="run-the-script"></a>Spuštění skriptu

Tento skript:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role Windows, včetně aktivační služby Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje modul IIS s možností zápisu. [Přečtěte si další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Azure Migrate.
- Vytvoří protokol a konfigurační soubory následujícím způsobem:
    - **Konfigurační soubory**:%ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**:%ProgramData%\Microsoft Azure\Logs

Spuštění skriptu:

1. Extrahujte soubor zip do složky na počítači, který bude hostovat zařízení. Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na počítači spusťte PowerShell s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller. ps1**následujícím způsobem:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="next-steps"></a>Další kroky

Po nasazení zařízení je třeba ho nakonfigurovat poprvé a zaregistrovat ho v Azure Migrate projektu.

- Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Nastavte zařízení pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
