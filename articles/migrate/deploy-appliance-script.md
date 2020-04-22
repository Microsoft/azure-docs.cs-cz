---
title: Nastavení zařízení Azure Migrate pomocí skriptu
description: Přečtěte si, jak nastavit zařízení Azure Migrate pomocí skriptu
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676299"
---
# <a name="set-up-an-appliance-with-a-script"></a>Nastavení zařízení se skriptem

V tomto článku [vytvořte zařízení Azure Migrate](deploy-appliance.md) pro vyhodnocení/migraci virtuálních počítačů VMware a virtuálních počítačích Hyper-V. Spuštění skriptu k vytvoření zařízení a ověření, že se můžete připojit k Azure. 

Zařízení pro virtuální počítače VMware a Hyper-V můžete nasadit pomocí skriptu nebo pomocí šablony, kterou stáhnete z webu Azure Portal. Použití skriptu je užitečné, pokud se vám nedaří vytvořit virtuální hod pomocí stažené šablony.

- Chcete-li použít šablonu, postupujte podle kurzů pro [vmware](tutorial-prepare-vmware.md) nebo [Hyper-V](tutorial-prepare-hyper-v.md).
- Chcete-li nastavit zařízení pro fyzické servery, můžete použít pouze skript. Postupujte podle [tohoto článku](how-to-set-up-appliance-physical.md).
- Pokud chcete nastavit zařízení v cloudu Azure Government, postupujte podle [tohoto článku](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Požadavky

Skript nastaví zařízení Azure Migrate na existujícím fyzickém počítači nebo virtuálním počítači.

- Počítač, který bude fungovat jako zařízení, musí být se systémem Windows Server 2016 s 32 GB paměti, osmi virtuálními procesory, přibližně 80 GB diskového úložiště a externím virtuálním přepínačem. Vyžaduje statickou nebo dynamickou IP adresu a přístup k internetu.
- Před nasazením zařízení zkontrolujte podrobné požadavky zařízení pro [virtuální zařízení VMware](migrate-appliance.md#appliance---vmware), [virtuální chody Hyper-V](migrate-appliance.md#appliance---hyper-v)a [fyzické servery](migrate-appliance.md#appliance---physical).
- Nespouštějte skript na existující zařízení Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Nastavení zařízení pro vmware

Chcete-li nastavit zařízení pro VMware, stáhnete soubor zip z portálu Azure a extrahujte obsah. Spuštění skriptu Prostředí PowerShell spustit webové aplikace zařízení. Nastavíte zařízení a nakonfigurujete jej poprvé. Potom zaregistrujete zařízení s projektem Azure Migrate.

### <a name="download-the-script"></a>Stáhněte si skript

1.  V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2.  V **Discover machines** > **Jsou vaše počítače virtualizované?**, vyberte **Ano, s hypervisorem VMWare vSphere**.
3.  Chcete-li stáhnout soubor zip, klepněte na tlačítko **Stáhnout**. 


### <a name="verify-file-security"></a>Ověření zabezpečení souborů

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spuštěním následujícího příkazu vygenerujte soubor hash pro zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Ověřte vygenerovanou hodnotu hash. Pro nejnovější verzi spotřebiče:

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e6de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>Spuštění skriptu

Zde je to, co skript dělá:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role systému Windows, včetně služby Aktivace systému Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje přepnutelný modul služby IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Migraci Azure.
- Vytvoří soubory protokolu a konfigurační soubory následujícím způsobem:
    - **Soubory konfigurace**: %ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**: %ProgramData%\Microsoft Azure\Protokoly

Spuštění skriptu:

1. Extrahujte soubor zip do složky v počítači, která bude hostitelem zařízení. Ujistěte se, že nespustíte skript na počítači na existujícízařízení Azure Migrate.
2. Spusťte powershell v počítači s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář prostředí PowerShell na složku obsahující obsah extrahovaný ze staženého zipového souboru.
4. Spusťte skript **AzureMigrateInstaller.ps1**takto:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Po úspěšném spuštění skriptu spustí webové aplikace zařízení, takže můžete nastavit zařízení. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptů na adrese C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Ověření přístupu

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) cloud.

## <a name="set-up-the-appliance-for-hyper-v"></a>Nastavte přístroj pro Hyper-V

Chcete-li nastavit zařízení pro Hyper-V, stáhněte si soubor zip z webu Azure portal a extrahujte obsah. Spuštění skriptu Prostředí PowerShell spustit webové aplikace zařízení. Nastavíte zařízení a nakonfigurujete jej poprvé. Potom zaregistrujete zařízení s projektem Azure Migrate.

### <a name="download-the-script"></a>Stáhněte si skript

1.  V **serverech cílů** > **migrace, které** > **Azure migruje: Vyhodnocení serveru**, klepněte na tlačítko **Objevit**.
2.  V **discover strojích** > **Jsou vaše počítače virtualizované?**, vyberte **Ano, s Technologiemi Hyper-V**.
3.  Chcete-li stáhnout soubor zip, klepněte na tlačítko **Stáhnout**. 


### <a name="verify-file-security"></a>Ověření zabezpečení souborů

Před nasazením zkontrolujte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spuštěním následujícího příkazu vygenerujte soubor hash pro zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ověřte generované hodnoty hash. Pro nejnovější verzi spotřebiče:

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e6de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>Spuštění skriptu

Zde je to, co skript dělá:

- Nainstaluje agenty a webovou aplikaci.
- Nainstaluje role systému Windows, včetně služby Aktivace systému Windows, služby IIS a prostředí PowerShell ISE.
- Stáhne a nainstaluje přepnutelný modul služby IIS. [Další informace](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klíč registru (HKLM) s trvalým nastavením pro Migraci Azure.
- Vytvoří soubory protokolu a konfigurační soubory následujícím způsobem:
    - **Soubory konfigurace**: %ProgramData%\Microsoft Azure\Config
    - **Soubory protokolu**: %ProgramData%\Microsoft Azure\Protokoly

Spuštění skriptu:

1. Extrahujte soubor zip do složky v počítači, která bude hostitelem zařízení. Ujistěte se, že nespustíte skript na počítači na existujícízařízení Azure Migrate.
2. Spusťte powershell v počítači s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář prostředí PowerShell na složku obsahující obsah extrahovaný ze staženého zipového souboru.
4. Spusťte skript **AzureMigrateInstaller.ps1**takto:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po úspěšném spuštění skriptu spustí webové aplikace zařízení, takže můžete nastavit zařízení. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptů na adrese C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Ověření přístupu

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [veřejný](migrate-appliance.md#public-cloud-urls) cloud.

## <a name="next-steps"></a>Další kroky

Po nasazení zařízení je potřeba ho poprvé nakonfigurovat a zaregistrovat v projektu Azure Migrate.

- Nastavte přístroj pro [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Nastavte přístroj pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
