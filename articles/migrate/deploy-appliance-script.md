---
title: Nastavení zařízení Azure Migrate pomocí skriptu
description: Naučte se, jak nastavit zařízení Azure Migrate pomocí skriptu.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 47b6b35e62d484b4d7a33f6a53796c59e01817fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102442"
---
# <a name="set-up-an-appliance-with-a-script"></a>Nastavení zařízení pomocí skriptu

Podle tohoto článku vytvořte [zařízení Azure Migrate](./migrate-appliance-architecture.md) pro účely posouzení/migrace virtuálních počítačů VMware a virtuálních počítačů Hyper-V. Spustíte skript pro vytvoření zařízení a ověříte, že se může připojit k Azure. 

Zařízení pro virtuální počítače VMware a Hyper-V můžete nasadit pomocí skriptu nebo pomocí šablony, kterou stáhnete z Azure Portal. Použití skriptu je užitečné, pokud nemůžete vytvořit virtuální počítač pomocí stažené šablony.

- Chcete-li použít šablonu, postupujte podle kurzů pro [VMware](tutorial-prepare-vmware.md) nebo [Hyper-V](tutorial-prepare-hyper-v.md).
- K nastavení zařízení pro fyzické servery můžete použít jenom skript. Postupujte podle [tohoto článku](how-to-set-up-appliance-physical.md).
- Pokud chcete zařízení nastavit v cloudu Azure Government, postupujte podle pokynů v [tomto článku](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Požadavky

Skript nastaví zařízení Azure Migrate na stávajícím fyzickém počítači nebo VIRTUÁLNÍm počítači.

- Počítač, který bude fungovat jako zařízení, musí splňovat následující požadavky na hardware a operační systém:

Scénář | Požadavky
--- | ---
VMware | Windows Server 2016 s 32 GB paměti, 8 vCPU, kolem 80 GB diskového úložiště
Hyper-V | Windows Server 2016, 16 GB paměti, 8 vCPU, přibližně 80 GB diskového úložiště
- Počítač taky potřebuje externí virtuální přepínač. Vyžaduje statickou nebo dynamickou IP adresu a přístup k Internetu.
- Před nasazením zařízení si přečtěte podrobný požadavky na zařízení pro [virtuální počítače VMware](migrate-appliance.md#appliance---vmware)a [virtuální počítače Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Nespouštějte skript na existujícím zařízení Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Nastavení zařízení pro VMware

Chcete-li nastavit zařízení pro VMware, Stáhněte si soubor zip s názvem AzureMigrateInstaller.zip [odsud a](https://go.microsoft.com/fwlink/?linkid=2105112)extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu Azure Migrate.


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Ověřte verzi nejnovějšího a skriptu pro veřejný cloud Azure:

    **Algoritmus** | **Stáhnout** | **SHA256**
    --- | --- | ---
    VMware (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0



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

1. Extrahujte soubor zip do složky na počítači, který bude hostovat zařízení. Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na počítači spusťte PowerShell s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1**následujícím způsobem:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="set-up-the-appliance-for-hyper-v"></a>Nastavení zařízení pro Hyper-V

Chcete-li nastavit zařízení pro technologii Hyper-V, Stáhněte si soubor zip s názvem [AzureMigrateInstaller.zip odsud a](https://go.microsoft.com/fwlink/?linkid=2105112)extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu Azure Migrate.


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ověřte verzi nejnovějšího a skriptu pro veřejný cloud Azure:

    **Scénář** | **Stáhnout** | **SHA256**
    --- | --- | ---
    Hyper-V (63,1 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2105112) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

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

1. Extrahujte soubor zip do složky na počítači, který bude hostovat zařízení. Ujistěte se, že nespouštíte skript na počítači v existujícím zařízení Azure Migrate.
2. Na počítači spusťte PowerShell s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1**následujícím způsobem:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) Cloud.

## <a name="next-steps"></a>Další kroky

Po nasazení zařízení je třeba ho nakonfigurovat poprvé a zaregistrovat ho v Azure Migrate projektu.

- Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Nastavte zařízení pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
