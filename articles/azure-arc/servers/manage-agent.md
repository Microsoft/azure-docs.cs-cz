---
title: Správa agenta Azure Arc pro servery (preview)
description: Tento článek popisuje různé úlohy správy, které budete obvykle provádět během životního cyklu Azure Arc pro servery agenta připojeného počítače.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308959"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Správa a údržba agenta připojeného stroje

Po počátečním nasazení Azure Arc pro servery (preview) agent connected machine pro Windows nebo Linux, budete muset překonfigurovat agenta, upgradovat nebo odebrat z počítače, pokud dosáhl fáze vyřazení v jeho životním cyklu. Tyto rutinní úlohy údržby můžete snadno spravovat ručně nebo prostřednictvím automatizace, což snižuje provozní chyby i náklady.

## <a name="upgrading-agent"></a>Inovace agenta

Agent azure connected machine pro Windows a Linux lze upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na vašich požadavcích. Následující tabulka popisuje metody podporované k provedení upgradu agenta.

| Operační systém | Metoda upgradu |
|------------------|----------------|
| Windows | Ručně<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper (Zypper)](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agent Windows

Chcete-li aktualizovat agenta v počítači se systémem Windows na nejnovější verzi, je agent k dispozici na webu Microsoft Update a lze jej nasadit pomocí stávajícího procesu správy aktualizací softwaru. Lze jej také spustit ručně z příkazového řádku, ze skriptu nebo jiného řešení `AzureConnectedMachine.msi`automatizace nebo z průvodce uživatelského rozhraní spuštěním . 

> [!NOTE]
> * Chcete-li inovovat agenta, musíte mít oprávnění *správce.*
> * Chcete-li provést ruční upgrade, je třeba nejprve stáhnout a zkopírovat balíček Instalační služby do složky na cílovém serveru nebo ze sdílené síťové složky. 

Pokud nejste obeznámeni s možnostmi příkazového řádku pro balíčky Instalační služby systému Windows, přečtěte si [standardní možnosti příkazového řádku Msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) a [možnosti příkazového řádku Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Inovace pomocí Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte **soubor AzureConnectedMachineAgent.msi** a spusťte Průvodce instalací.

Průvodce instalací zjistí, zda existuje předchozí verze, a poté automaticky provede upgrade agenta. Po dokončení upgradu se Průvodce instalací automaticky zavře.

#### <a name="to-upgrade-from-the-command-line"></a>Upgrade z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Chcete-li agenta upgradovat tiše a `C:\Support\Logs` vytvořit soubor protokolu instalace ve složce, spusťte následující příkaz.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linuxagent

Chcete-li aktualizovat agenta na počítači s Linuxem na nejnovější verzi, zahrnuje dva příkazy. Jeden příkaz k aktualizaci indexu místního balíčku se seznamem nejnovějších dostupných balíčků z úložišť a jeden příkaz pro upgrade místního balíčku. 

> [!NOTE]
> Chcete-li inovovat agenta, musíte mít *oprávnění root* access nebo s účtem, který má zvýšená práva pomocí Sudo.

#### <a name="upgrade-ubuntu"></a>Upgrade Ubuntu

1. Chcete-li aktualizovat místní index balíčku nejnovějšími změnami provedenými v úložištích, spusťte následující příkaz:

    ```bash
    apt update
    ```

2. Chcete-li systém upgradovat, spusťte následující příkaz:

    ```bash
    apt upgrade
    ```

Do souboru protokolu jsou zaznamenány akce příkazu `/var/log/dpkg.log` [apt,](https://help.ubuntu.com/lts/serverguide/apt.html) například instalace a odebrání balíčků.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat / Centos / Amazon Linux

1. Chcete-li aktualizovat místní index balíčku nejnovějšími změnami provedenými v úložištích, spusťte následující příkaz:

    ```bash
    yum check-update
    ```

2. Chcete-li systém upgradovat, spusťte následující příkaz:

    ```bash
    yum update
    ```

Akce příkazu [yum,](https://access.redhat.com/articles/yum-cheat-sheet) jako je instalace a odebrání balíčků, jsou zaznamenány v souboru `/var/log/yum.log` protokolu. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade SUSE Linux Enterprise

1. Chcete-li aktualizovat místní index balíčku nejnovějšími změnami provedenými v úložištích, spusťte následující příkaz:

    ```bash
    zypper refresh
    ```

2. Chcete-li systém upgradovat, spusťte následující příkaz:

    ```bash
    zypper update
    ```

Do souboru protokolu jsou zaznamenány akce příkazu `/var/log/zypper.log` [zypper,](https://en.opensuse.org/Portal:Zypper) například instalace a odebrání balíčků. 

## <a name="about-the-azcmagent-tool"></a>O nástroji Azcmagent

Nástroj Azcmagent (Azcmagent.exe) se používá ke konfiguraci agenta Azure Arc pro servery (náhled) připojeného počítače během instalace nebo k úpravě počáteční konfigurace agenta po instalaci. Azcmagent.exe poskytuje parametry příkazového řádku pro přizpůsobení agenta a zobrazení jeho stavu:

* **Připojení** – připojení počítače k Azure Arc

* **Odpojit** – odpojení počítače od Azure Arc

* **Opětovné připojení** – opětovné připojení odpojeného počítače k Azure Arc

* **Zobrazit** – zobrazení stavu agenta a jeho vlastností konfigurace (název skupiny prostředků, ID předplatného, verze atd.), což může pomoci při řešení problému s agentem.

* **-h nebo --help** - Zobrazuje dostupné parametry příkazového řádku.

    Chcete-li například zobrazit podrobnou nápovědu `azcmagent reconnect -h`k parametru **Znovu připojit,** zadejte . 

* **-v nebo --verbose** - Povolit podrobné protokolování

Můžete provádět **připojit**, **odpojit**a **znovu připojit** ručně, když přihlášen interaktivně nebo automatizovat pomocí stejného instančního objektu, který jste použili k připojení více agentů nebo s [tokenem přístupu](../../active-directory/develop/access-tokens.md)platformy identit Microsoft . Pokud jste nepoužili instanční objekt k registraci počítače s Azure Arc pro servery (náhled), najdete v následujícím [článku](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) k vytvoření instančního objektu.

### <a name="connect"></a>Připojení

Tento parametr určuje prostředek ve Správci prostředků Azure představující počítač se vytvoří v Azure. Prostředek je v zadané skupině předplatného a prostředků a data o počítači `--location` se ukládají v oblasti Azure určené nastavením. Výchozí název prostředku je název hostitele tohoto počítače, pokud není zadán.

Certifikát odpovídající systémově přiřazené identitě počítače je pak stažen a uložen místně. Po dokončení tohoto kroku začne služba metadat azure připojeného počítače a agent konfigurace hosta synchronizaci s Azure Arc pro servery (náhled).

Chcete-li se připojit pomocí instančního objektu služby, spusťte následující příkaz:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Chcete-li se připojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Chcete-li se spojit se zvýšenými přihlašovacími přihlašovacími údaji (interaktivními), spusťte následující příkaz:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Odpojit

Tento parametr určuje prostředek ve Správci prostředků Azure představující počítač se odstraní v Azure. Neodstraní agenta z počítače, musí to být provedeno jako samostatný krok. Po odpojení počítače, pokud chcete znovu zaregistrovat pomocí Azure Arc pro `azcmagent connect` servery (preview), použijte tak nový prostředek se vytvoří pro něj v Azure.

Chcete-li se odpojit pomocí instančního objektu služby, spusťte následující příkaz:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Chcete-li se odpojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent disconnect --access-token <accessToken>`

Chcete-li se odpojit se zvýšenými přihlašovacími údaji (interaktivními), spusťte následující příkaz:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Znovu připojit

Tento parametr znovu připojí již registrovaný nebo připojený počítač s Azure Arc pro servery (náhled). To může být nezbytné, pokud byl stroj vypnut, alespoň 45 dní, aby jeho platnost platnost platnosti certifikátu vypršela. Tento parametr používá možnosti ověřování k načtení nových přihlašovacích údajů odpovídajících prostředku Azure Resource Manager představující tento počítač.

Tento příkaz vyžaduje vyšší oprávnění než role [Připojení k počítači Azure.](overview.md#required-permissions)

Chcete-li se znovu připojit pomocí instančního objektu služby, spusťte následující příkaz:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Chcete-li se znovu připojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent reconnect --access-token <accessToken>`

Chcete-li se znovu připojit ke zvýšeným přihlašovacím pověřením (interaktivním), spusťte následující příkaz:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Odebrání agenta

Chcete-li odinstalovat agenta počítače S Windows nebo Linux Connected Machine, proveďte jednu z následujících metod. Odebráníagenta nezruší registraci počítače pomocí arc pro servery (náhled), jedná se o samostatný proces, který provádíte, když už nemusíte spravovat počítač v Azure.

### <a name="windows-agent"></a>Agent Windows

Obě následující metody agenta odeberou, ale neodeberou složku *C:\Program Files\AzureConnectedMachineAgent* v počítači.

#### <a name="uninstall-from-control-panel"></a>Odinstalace z Ovládacích panelů

1. Chcete-li odinstalovat agenta systému Windows ze zařízení, postupujte takto:

    a. Přihlaste se k počítači pomocí účtu, který má oprávnění správce.  
    b. V **Ovládacích panelech**vyberte **položku Programy a funkce**.  
    c. V **části Programy a funkce**vyberte Azure Connected Machine **Agent**, vyberte **Odinstalovat**a pak vyberte **Ano**.  

    >[!NOTE]
    > Průvodce nastavením agenta můžete spustit také poklepáním na instalační balíček **AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Odinstalace z příkazového řádku

Chcete-li odinstalovat agenta ručně z příkazového řádku nebo použít automatizovanou metodu, například skript, můžete použít následující příklad. Nejprve je třeba načíst kód produktu, což je identifikátor GUID, který je hlavním identifikátorem balíčku aplikace, z operačního systému. Odinstalace se provádí pomocí příkazového řádku Msiexec.exe - `msiexec /x {Product Code}`.
    
1. Otevřete Editor registru.

2. V části `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`Klíč registru vyhledejte a zkopírujte identifikátor GUID kódu produktu.

3. Potom můžete odinstalovat agenta pomocí Msiexec pomocí následujících příkladů:

   * Z typu příkazového řádku:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Můžete provést stejné kroky pomocí Prostředí PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linuxagent

> [!NOTE]
> Chcete-li odinstalovat agenta, musíte mít *oprávnění root* access nebo s účtem, který má zvýšená práva pomocí Sudo.

Chcete-li odinstalovat agenta Linuxu, příkaz, který chcete použít, závisí na operačním systému Linux.

- Pro Ubuntu spusťte následující příkaz:

    ```bash
    sudo apt purge azcmagent
    ```

- Pro RHEL, CentOS a Amazon Linux spusťte následující příkaz:

    ```bash
    sudo yum remove azcmagent
    ```

- Pro SLES spusťte následující příkaz:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Zrušit registraci počítače

Pokud plánujete zastavit správu počítače s podpůrnými službami v Azure, proveďte následující kroky k odregistru počítače s Arc pro servery (náhled). Tyto kroky můžete provést před nebo po odebrání agenta připojeného počítače ze zařízení.

1. Otevřete Azure Arc pro servery (preview) na [webu Azure Portal](https://aka.ms/hybridmachineportal).

2. Vyberte zařízení v seznamu, vyberte tři tečky (**...**) a pak vyberte **Odstranit**.
