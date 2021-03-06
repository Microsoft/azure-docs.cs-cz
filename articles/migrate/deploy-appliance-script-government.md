---
title: Nastavení zařízení Azure Migrate v Azure Government
description: Přečtěte si, jak nastavit zařízení Azure Migrate v Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714789"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Nastavení zařízení v Azure Government 

Podle tohoto článku nasaďte [zařízení Azure Migrate](./migrate-appliance-architecture.md) pro servery v prostředí VMware, serverech v Hyper-V a fyzických serverech v cloudu Azure Government. Spustíte skript pro vytvoření zařízení a ověříte, že se může připojit k Azure. Pokud chcete nastavit zařízení ve veřejném cloudu, postupujte podle pokynů v [tomto článku](deploy-appliance-script.md).


> [!NOTE]
> Možnost nasazení zařízení pomocí šablony (pro servery v prostředí VMware a prostředí technologie Hyper-V) není v Azure Government podporovaná.


## <a name="prerequisites"></a>Požadavky

Skript nastaví zařízení Azure Migrate na stávajícím fyzickém serveru nebo na virtualizovaném serveru.

- Na serveru, který bude fungovat jako zařízení, musí být spuštěný Windows Server 2016 s 32 GB paměti, osmi vCPU, okolo 80 GB diskového úložiště a externím virtuálním přepínačem. Vyžaduje statickou nebo dynamickou IP adresu. 
- Před nasazením zařízení si přečtěte podrobné požadavky na zařízení pro [servery v VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v)a [fyzických serverech](migrate-appliance.md#appliance---physical).
- Nespouštějte skript na existujícím zařízení Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Nastavení zařízení pro VMware

Pokud chcete nastavit zařízení pro VMware, Stáhněte si z Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu.

### <a name="download-the-script"></a>Stáhnout skript

1. V **cíli migrace** se  >  **systémy Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** klikněte na **zjistit**.
2. V možnosti **zjišťovat Server**  >  **jsou vaše servery virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3. Pro stažení souboru ZIP klikněte na **Stáhnout**.

### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na serveru, na který jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Ověřte nejnovější verzi a hodnotu hash zařízení:

    **Algoritmus** | **Stáhnout** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


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

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení. Ujistěte se, že nespouštíte skript na serveru s existujícím zařízením Azure Migrate.
2. Na serveru spusťte PowerShell s oprávněními správce (zvýšené oprávnění).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1** následujícím způsobem:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [cloudy státní správy](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Nastavení zařízení pro Hyper-V

Pokud chcete nastavit zařízení pro Hyper-V, Stáhněte ze Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu.

### <a name="download-the-script"></a>Stáhnout skript

1.  V **cíli migrace** se  >  **systémy Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** klikněte na **zjistit**.
2.  V rozevíracích **seznamech servery**  >  **jsou vaše servery virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3.  Pro stažení souboru ZIP klikněte na **Stáhnout**. 


### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na serveru, na který jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Ověřte nejnovější verzi a hodnotu hash zařízení:

    **Scénář** | **Stáhnout** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

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

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení. Ujistěte se, že nespouštíte skript na serveru, na kterém běží stávající zařízení Azure Migrate.
2. Na serveru spusťte PowerShell s oprávněními správce (zvýšené oprávnění).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1** následujícím způsobem: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [cloudy státní správy](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Nastavení zařízení pro fyzické servery

Pokud chcete nastavit zařízení pro VMware, Stáhněte si z Azure Portal soubor zip a extrahujte obsah. Spustíte PowerShellový skript, který spustí webovou aplikaci zařízení. Zařízení nastavíte a poprvé ho nakonfigurujete. Pak zaregistrujete zařízení do projektu.

### <a name="download-the-script"></a>Stáhnout skript

1. V **cíli migrace** se  >  **systémy Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** klikněte na **zjistit**.
2. V možnosti **zjišťovat servery**  >  **jsou vaše servery virtualizované?** vyberte **nevirtualizované/jiné**.
3. Pro stažení souboru ZIP klikněte na **Stáhnout**.

### <a name="verify-file-security"></a>Ověřit zabezpečení souboru

Před nasazením souboru ZIP ověřte, zda je soubor zip zabezpečený.

1. Na serverech, na které jste stáhli soubor, otevřete okno příkazového řádku správce.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor zip.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklad: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Ověřte nejnovější verzi a hodnotu hash zařízení:

    **Scénář** | **Stáhnout** _ | _ *Hodnota hash**
    --- | --- | ---
    Fyzický (85 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

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

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení. Ujistěte se, že nespouštíte skript na serveru, na kterém běží stávající zařízení Azure Migrate.
2. Na serveru spusťte PowerShell s oprávněními správce (zvýšené oprávnění).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1** následujícím způsobem:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Po úspěšném spuštění skriptu se spustí webová aplikace zařízení, aby bylo možné zařízení nastavit. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Ověřit přístup

Ujistěte se, že se zařízení může připojit k adresám URL Azure pro [cloudy státní správy](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Další kroky

Po nasazení zařízení je třeba ho nakonfigurovat poprvé a zaregistrovat ho v projektu.

- Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Nastavte zařízení pro [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Nastavte zařízení pro [fyzické servery](how-to-set-up-appliance-physical.md).
