---
title: Správa agenta Azure ARC pro servery (Preview)
description: Tento článek popisuje různé úlohy správy, které obvykle provedete během životního cyklu Azure ARC pro agenty počítačů připojených k serveru.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/30/2020
ms.topic: conceptual
ms.openlocfilehash: 73ece3f1bc8d5e88d4c1c37e1040f2494230e4ee
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809591"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Správa a údržba agenta připojeného počítače

Po počátečním nasazení služby Azure ARC pro servery (Preview) připojeného agenta počítače pro systém Windows nebo Linux bude pravděpodobně nutné překonfigurovat agenta, provést upgrade nebo jej odebrat z počítače, pokud dosáhli fáze vyřazení v životním cyklu. Tyto rutinní úlohy údržby můžete snadno spravovat ručně nebo prostřednictvím automatizace, což snižuje provozní chybu i výdaje.

## <a name="upgrading-agent"></a>Upgrade agenta

Agenta připojeného počítače Azure pro Windows a Linux se dá upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na vašich požadavcích. Následující tabulka popisuje metody podporované pro provedení upgradu agenta.

| Operační systém | Metoda upgradu |
|------------------|----------------|
| Windows | Ručně<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypperu](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yumu](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Agent Windows

Balíček aktualizace pro agenta připojeného počítače pro Windows je dostupný z těchto počítačů:

* Microsoft Update

* [Katalog Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx)

* [Balíček Windows agent Instalační služba systému Windows](https://aka.ms/AzureConnectedMachineAgent) z webu Microsoft Download Center.

Agenta je možné upgradovat podle různých metod, aby bylo možné podporovat proces správy aktualizací softwaru. Mimo získání z Microsoft Update můžete stáhnout a spustit ručně z příkazového řádku, ze skriptu nebo jiného řešení automatizace nebo z Průvodce uživatelským rozhraním spuštěním `AzureConnectedMachine.msi` .

> [!NOTE]
> * Chcete-li upgradovat agenta, je nutné mít oprávnění *správce* .
> * Pokud chcete upgradovat ručně, musíte nejdřív stáhnout a zkopírovat instalační balíček do složky na cílovém serveru nebo ze sdílené síťové složky. 

Pokud neznáte možnosti příkazového řádku pro Instalační služba systému Windows balíčky, Projděte si možnosti příkazového řádku nástroje [msiexec Standard](/windows/win32/msi/standard-installer-command-line-options) a [Možnosti příkazového](/windows/win32/msi/command-line-options)řádku programu Msiexec.

#### <a name="to-upgrade-using-the-setup-wizard"></a>Upgrade pomocí Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte **AzureConnectedMachineAgent.msi** pro spuštění Průvodce instalací nástroje.

Průvodce instalací zjistí, zda existuje předchozí verze, a poté automaticky provede upgrade agenta. Po dokončení upgradu se Průvodce instalací automaticky zavře.

#### <a name="to-upgrade-from-the-command-line"></a>Upgrade z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Chcete-li upgradovat agenta tiše a vytvořit soubor protokolu instalace ve `C:\Support\Logs` složce, spusťte následující příkaz.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agent pro Linux

Chcete-li aktualizovat agenta na počítači se systémem Linux na nejnovější verzi, zahrnuje dva příkazy. Jeden příkaz pro aktualizaci indexu místního balíčku se seznamem nejnovějších dostupných balíčků z úložišť a jedním příkazem k upgradu místního balíčku.

Nejnovější balíček agenta si můžete stáhnout z [úložiště balíčků](https://packages.microsoft.com/)Microsoftu.

> [!NOTE]
> Chcete-li upgradovat agenta, je nutné mít oprávnění *root* Access nebo s účtem, který má zvýšená oprávnění s použitím sudo.

#### <a name="upgrade-ubuntu"></a>Upgradovat Ubuntu

1. Pokud chcete aktualizovat index místního balíčku nejnovějšími změnami provedenými v úložištích, spusťte následující příkaz:

    ```bash
    apt update
    ```

2. Chcete-li upgradovat systém, spusťte následující příkaz:

    ```bash
    apt upgrade
    ```

Akce příkazu [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , jako je instalace a odebrání balíčků, se zaznamenávají do `/var/log/dpkg.log` souboru protokolu.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat/CentOS/Amazon Linux

1. Pokud chcete aktualizovat index místního balíčku nejnovějšími změnami provedenými v úložištích, spusťte následující příkaz:

    ```bash
    yum check-update
    ```

2. Chcete-li upgradovat systém, spusťte následující příkaz:

    ```bash
    yum update
    ```

Akce příkazu [Yumu](https://access.redhat.com/articles/yum-cheat-sheet) , jako je instalace a odebrání balíčků, se zaznamenávají do `/var/log/yum.log` souboru protokolu. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade SUSE Linux Enterprise

1. Pokud chcete aktualizovat index místního balíčku nejnovějšími změnami provedenými v úložištích, spusťte následující příkaz:

    ```bash
    zypper refresh
    ```

2. Chcete-li upgradovat systém, spusťte následující příkaz:

    ```bash
    zypper update
    ```

Akce příkazu [zypperu](https://en.opensuse.org/Portal:Zypper) , jako je instalace a odebrání balíčků, se zaznamenávají do `/var/log/zypper.log` souboru protokolu. 

## <a name="about-the-azcmagent-tool"></a>O nástroji Azcmagent

Nástroj Azcmagent (Azcmagent.exe) se používá ke konfiguraci agenta Azure ARC pro servery (Preview) připojeného počítače během instalace nebo po instalaci změnit počáteční konfiguraci agenta. Azcmagent.exe poskytuje parametry příkazového řádku pro přizpůsobení agenta a zobrazení jeho stavu:

* **Připojení** – připojení počítače k Arc Azure

* **Odpojit** – odpojení počítače od Arc Azure

* **Znovu připojit** -pro opětovné připojení odpojeného počítače ke ARC Azure

* **Zobrazit** – zobrazit stav agenta a jeho vlastnosti konfigurace (název skupiny prostředků, ID předplatného, verze atd.), která může pomáhat při řešení potíží s agentem.

* **-h nebo--help** -zobrazí dostupné parametry příkazového řádku.

    Pokud například chcete zobrazit podrobnou nápovědu pro parametr **reconnect** , zadejte `azcmagent reconnect -h` . 

* **-v nebo--verbose** – Povolit podrobné protokolování

**Připojení**, **odpojení**a opětovné **připojení** můžete provést ručně, pokud jste přihlášeni interaktivně, nebo automatizovat pomocí stejného instančního objektu, který jste použili k registraci více agentů nebo pomocí [přístupového tokenu](../../active-directory/develop/access-tokens.md)platformy Microsoft identity. Pokud jste nepoužívali instanční objekt k registraci počítače pomocí ARC Azure pro servery (Preview), přečtěte si následující [článek](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a vytvořte instanční objekt.

>[!NOTE]
>Aby bylo možné spustit **azcmagent**, musíte mít oprávnění *root* Access pro počítače se systémem Linux.

### <a name="connect"></a>Připojit

Tento parametr určuje prostředek v Azure Resource Manager, který představuje počítač vytvořený v Azure. Prostředek je v předplatném a v zadané skupině prostředků a data o počítači se ukládají v oblasti Azure určené `--location` nastavením. Výchozí název prostředku je název hostitele tohoto počítače, pokud není zadán.

Certifikát, který odpovídá identitě přiřazené systémem počítače, se pak stáhne a uloží místně. Po dokončení tohoto kroku se v počítači připojeném k Azure Metadata Service a Agent konfigurace hosta zahájí synchronizaci se službou Azure ARC pro servery (Preview).

Pokud se chcete připojit pomocí instančního objektu, spusťte následující příkaz:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Pokud se chcete připojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Pokud se chcete připojit pomocí přihlášených přihlašovacích údajů se zvýšenými oprávněními (Interactive), spusťte následující příkaz:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Odpojit

Tento parametr určuje prostředek v Azure Resource Manager, který představuje počítač odstraněný v Azure. Neodstraní z počítače agenta, tento krok se musí provést v samostatném kroku. Pokud je počítač odpojený, pokud ho chcete znovu zaregistrovat u služby Azure ARC pro servery (verze Preview), použijte `azcmagent connect` proto, aby byl pro něj vytvořen nový prostředek v Azure.

Pokud se chcete odpojit pomocí instančního objektu, spusťte následující příkaz:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Pokud chcete odpojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent disconnect --access-token <accessToken>`

Pokud se chcete odpojit od přihlašovacích údajů se zvýšenými oprávněními (interaktivní), spusťte následující příkaz:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Znovu připojit

> [!WARNING]
> `reconnect`Příkaz je zastaralý a neměl by se používat. Příkaz se odebere v budoucí verzi agenta a stávající agenti se nebude moct dokončit žádost o opětovné připojení. Místo toho [odpojte](#disconnect) počítač a znovu ho [Připojte](#connect) .

Tento parametr znovu připojí již registrovaný nebo připojený počítač ke službě Azure ARC pro servery (Preview). To může být nutné v případě, že je počítač vypnutý, minimálně 45 dní, aby jeho platnost jeho certifikátu vypršela. Tento parametr používá možnosti ověřování, které jsou k dispozici pro načtení nových přihlašovacích údajů odpovídajících prostředku Azure Resource Manager, který představuje tento počítač.

Tento příkaz vyžaduje vyšší oprávnění než role registrace [počítače připojeného k Azure](agent-overview.md#required-permissions) .

Pokud se chcete znovu připojit pomocí instančního objektu, spusťte následující příkaz:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Pokud se chcete znovu připojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent reconnect --access-token <accessToken>`

Pokud se chcete znovu připojit pomocí přihlašovacích údajů se zvýšenými oprávněními (interaktivní), spusťte následující příkaz:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Odebrat agenta

Proveďte jednu z následujících metod k odinstalaci agenta připojeného počítače s Windows nebo Linux z počítače. Odebrání agenta zruší registraci počítače pomocí ARC pro servery (Preview), jedná se o samostatný proces, který provedete, když už nepotřebujete spravovat počítač v Azure.

### <a name="windows-agent"></a>Agent Windows

Obě následující metody odeberou agenta, ale neodstraňují složku *C:\Program Files\AzureConnectedMachineAgent* v počítači.

#### <a name="uninstall-from-control-panel"></a>Odinstalace z ovládacích panelů

1. Chcete-li odinstalovat agenta systému Windows z počítače, postupujte takto:

    a. Přihlaste se k počítači pomocí účtu, který má oprávnění správce.  
    b. V **Ovládacích panelech**vyberte **programy a funkce**.  
    c. V **programech a funkcích**vyberte **Azure Connected Machine agent**, vyberte **odinstalovat**a pak vyberte **Ano**.  

    >[!NOTE]
    > Průvodce instalací agenta můžete také spustit dvojitým kliknutím na balíček Instalační služby **AzureConnectedMachineAgent.msi** .

#### <a name="uninstall-from-the-command-line"></a>Odinstalace z příkazového řádku

K ručnímu odinstalování agenta z příkazového řádku nebo k použití automatizované metody, jako je například skript, můžete použít následující příklad. Nejprve musíte načíst kód produktu, což je identifikátor GUID, který je hlavním identifikátorem balíčku aplikace, z operačního systému. Odinstalace se provádí pomocí příkazového řádku Msiexec.exe – `msiexec /x {Product Code}` .

1. Otevřete Editor registru.

2. V části klíč registru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` vyhledejte a zkopírujte identifikátor GUID kódu produktu.

3. Pak můžete agenta odinstalovat pomocí následujících příkladů pomocí programu Msiexec:

   * Z příkazového řádku zadejte:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Pomocí prostředí PowerShell můžete provádět stejné kroky:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agent pro Linux

> [!NOTE]
> Chcete-li odinstalovat agenta, je nutné mít oprávnění *root* Access nebo s účtem, který má zvýšená oprávnění s použitím sudo.

Pokud chcete odinstalovat agenta pro Linux, použitý příkaz závisí na operačním systému Linux.

- V případě Ubuntu spusťte následující příkaz:

    ```bash
    sudo apt purge azcmagent
    ```

- Pro RHEL, CentOS a Amazon Linux spusťte následující příkaz:

    ```bash
    sudo yum remove azcmagent
    ```

- V případě SLES spusťte následující příkaz:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Zrušit registraci počítače

Pokud plánujete ukončit správu počítače s podpůrnými službami v Azure, proveďte následující kroky, abyste zrušili registraci počítače pomocí ARC pro servery (Preview). Tyto kroky můžete provést buď před, nebo po odebrání agenta připojeného počítače z počítače.

1. Kliknutím na [Azure Portal](https://aka.ms/hybridmachineportal)otevřete položku Azure ARC pro servery (Preview).

2. Vyberte počítač v seznamu, vyberte tři tečky (**...**) a pak vyberte **Odstranit**.

## <a name="update-or-remove-proxy-settings"></a>Aktualizovat nebo odebrat nastavení proxy serveru

Chcete-li nakonfigurovat agenta ke komunikaci se službou prostřednictvím proxy server nebo tuto konfiguraci po nasazení odebrat, nebo použijte k dokončení této úlohy jednu z následujících metod.

### <a name="windows"></a>Windows

Chcete-li nastavit proměnnou prostředí proxy server, spusťte následující příkaz:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Chcete-li nakonfigurovat agenta tak, aby přestal komunikovat prostřednictvím proxy server, spusťte následující příkaz, který odebere proxy server proměnnou prostředí a restartuje službu agenta:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Pokud chcete nastavit proxy server, spusťte následující příkaz z adresáře, do kterého jste stáhli instalační balíček agenta:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Chcete-li nakonfigurovat agenta tak, aby přestal komunikovat prostřednictvím proxy server, odeberte konfiguraci proxy spuštěním následujícího příkazu:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Další kroky

- Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

- Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je nutný, pokud chcete aktivně monitorovat operační systém a úlohy běžící v počítači, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-intro.md).