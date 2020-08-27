---
title: Nastavení zařízení Azure Migrate v Azure Government
description: Přečtěte si, jak nastavit zařízení Azure Migrate v Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: ffea966c58909ecaab0da13a4204295ecb193895
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936788"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Nastavení zařízení v Azure Government 

Podle tohoto článku nasaďte [Azure Migrate zařízení](./migrate-appliance-architecture.md) pro virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery v cloudu Azure Government. Spustíte skript pro vytvoření zařízení a ověříte, že se může připojit k Azure. Pokud chcete nastavit zařízení ve veřejném cloudu, postupujte podle pokynů v [tomto článku](deploy-appliance-script.md).


> [!NOTE]
> Možnost nasadit zařízení pomocí šablony (pro virtuální počítače VMware a virtuální počítače Hyper-V) není v Azure Government podporovaná.


## <a name="prerequisites"></a>Předpoklady

Skript nastaví zařízení Azure Migrate na stávajícím fyzickém počítači nebo VIRTUÁLNÍm počítači.

- Počítač, který bude fungovat jako zařízení, musí používat Windows Server 2016, 32 GB paměti, 8 vCPU, přibližně 80 GB diskového úložiště a externí virtuální přepínač. Vyžaduje statickou nebo dynamickou IP adresu a přístup k Internetu.
- Před nasazením zařízení si přečtěte podrobný požadavky na zařízení pro [virtuální počítače VMware](migrate-appliance.md#appliance---vmware), [virtuální počítače Hyper-V](migrate-appliance.md#appliance---hyper-v)a [fyzické servery](migrate-appliance.md#appliance---physical).
- Nespouštějte skript na existujícím zařízení Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Nastavení zařízení pro VMware

Pokud chcete nastavit zařízení pro VMware, Stáhněte si z Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu Azure Migrate.

### <a name="download-the-script"></a>Stáhnout skript

1.  V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2.  V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3.  Pro stažení souboru ZIP klikněte na **Stáhnout**. 


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Ověřte nejnovější verzi a hodnotu hash zařízení:

    **Algoritmus** | **Stáhnout** | **SHA256**
    --- | --- | ---
    VMware (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140337) | 31b1bfdd4fc29b3eb923c7c6e7a898af79b7cac0404426bea18809def2284188


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
4. Spusťte skript **AzureMigrateInstaller.ps1**následujícím způsobem: 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [cloudy státní správy](migrate-appliance.md#government-cloud-urls).


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
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Ověřte nejnovější verzi a hodnotu hash zařízení:

    **Scénář** | **Stáhnout** | **SHA256**
    --- | --- | ---
    Hyper-V (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

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
4. Spusťte skript **AzureMigrateInstaller.ps1**následujícím způsobem: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [cloudy státní správy](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Nastavení zařízení pro fyzické servery

Pokud chcete nastavit zařízení pro VMware, Stáhněte si z Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu Azure Migrate.

### <a name="download-the-script"></a>Stáhnout skript

1.  V Azure Migrate **cíle migrace**  >  na**servery**  >  **: vyhodnocování serveru**klikněte na **zjistit**.
2.  V rámci **zjišťování počítačů**  >  **jsou vaše počítače virtualizované?** vyberte **nevirtualizované/jiné**.
3.  Pro stažení souboru ZIP klikněte na **Stáhnout**. 


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Ověřte nejnovější verzi a hodnotu hash zařízení:

    **Scénář** | **Stáhnout*** | **Hodnota hash**
    --- | --- | ---
    Fyzický (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140338) | 1545f9ce8874cedef6347c1a1332f8b5eabd6811a017440a2382525fb0430309
          

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
4. Spusťte skript **AzureMigrateInstaller.ps1**následujícím způsobem: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [cloudy státní správy](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Další kroky

Po nasazení zařízení je třeba ho nakonfigurovat poprvé a zaregistrovat ho v Azure Migrate projektu.

- Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Nastavte zařízení pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Nastavte zařízení pro [fyzické servery](how-to-set-up-appliance-physical.md).
