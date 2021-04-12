---
title: Správa agenta serverů s podporou ARC Azure
description: Tento článek popisuje různé úlohy správy, které obvykle provedete během životního cyklu serverů s podporou ARC Azure, které jsou agentem počítače připojené.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 36ae081f939cbf865db7755a2f766a7ccd87d619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587629"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Správa a údržba agenta připojeného počítače

Po počátečním nasazení serverů s podporou ARC Azure, které jsou připojené k počítači pro Windows nebo Linux, možná budete muset agenta překonfigurovat, upgradovat ho nebo ho odebrat z počítače. Tyto rutinní úlohy údržby můžete snadno spravovat ručně nebo prostřednictvím automatizace, což snižuje provozní chybu i výdaje.

## <a name="before-uninstalling-agent"></a>Před odinstalací agenta

Před odebráním agenta připojeného počítače ze serveru s povoleným obloukem zvažte následující skutečnosti, aby nedocházelo k neočekávaným problémům nebo přidaným nákladům na účet Azure:

* Pokud jste nasadili rozšíření virtuálních počítačů Azure na povolený Server a odebrali jste agenta připojeného počítače nebo odstraníte prostředek, který představuje server s podporou ARC ve skupině prostředků, budou tyto přípony nadále spuštěné a musí provádět jejich normální fungování.

* Pokud odstraníte prostředek, který představuje server s povoleným ARC ve vaší skupině prostředků, ale rozšíření virtuálního počítače nebudete odinstalováni, budete moci spravovat nainstalovaná rozšíření virtuálního počítače i při jejich opětovné registraci.

U serverů nebo počítačů, které už nechcete spravovat se servery s podporou ARC Azure, je potřeba pomocí těchto kroků úspěšně zastavit jejich správu:

1. Odeberte rozšíření virtuálních počítačů z počítače nebo serveru. Níže jsou uvedené kroky.

2. Odpojte počítač od Arc Azure pomocí jedné z následujících metod:

    * Spuštění `azcmagent disconnect` příkazu na počítači nebo na serveru.

    * Z vybraného serveru s povolenou příponou ARC v Azure Portal vyberte z horního panelu možnost **Odstranit** .

    * Použití rozhraní příkazového [řádku Azure](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) nebo [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource). Pro `ResourceType` použití parametru `Microsoft.HybridCompute/machines` .

3. [Odinstalujte agenta](#remove-the-agent) z počítače nebo serveru podle následujících kroků.

## <a name="renaming-a-machine"></a>Přejmenování počítače

Když změníte název počítače se systémem Linux nebo Windows připojeného k serverům s podporou ARC Azure, nový název se nerozpozná automaticky, protože název prostředku v Azure je neměnný. Stejně jako u jiných prostředků Azure je nutné odstranit prostředek a znovu ho vytvořit, aby bylo možné použít nový název.

Pro servery s povoleným obloukem před přejmenováním počítače je nutné před pokračováním odebrat rozšíření virtuálního počítače.

> [!NOTE]
> I když jsou nainstalovaná rozšíření i nadále spuštěná a po dokončení tohoto postupu provádějí jejich normální činnost, nebudete je moct spravovat. Pokud se pokusíte znovu nasadit rozšíření na počítači, může docházet k nepředvídatelnému chování.

> [!WARNING]
> Doporučujeme, abyste se vyhnuli přejmenování názvu počítače počítače a provádět tento postup pouze v případě, že je to nezbytně nutné.

1. Auditujte rozšíření virtuálních počítačů nainstalované v počítači a poznamenejte si jejich konfiguraci pomocí [Azure CLI](manage-vm-extensions-cli.md#list-extensions-installed) nebo použijte [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed).

2. Pomocí rozhraní příkazového [řádku Azure](manage-vm-extensions-cli.md#remove-an-installed-extension)nebo pomocí [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)odeberte rozšíření virtuálních počítačů nainstalovaná z [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension).

3. Pomocí nástroje **azcmagent** s parametrem [odpojení](manage-agent.md#disconnect) odpojte počítač od Arc Azure a odstraňte prostředek počítače z Azure. Odpojení počítače od serverů s podporou ARC neodebere agenta připojeného počítače a nebudete muset agenta odebírat jako součást tohoto procesu. Tuto možnost můžete spustit ručně během interaktivního přihlášení nebo automatizovat pomocí stejného instančního objektu, který jste použili k připojení více agentů, nebo pomocí [přístupového tokenu](../../active-directory/develop/access-tokens.md)platformy Microsoft identity. Pokud jste nepoužívali instanční objekt k registraci počítače se servery s podporou ARC Azure, přečtěte si následující [článek](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a vytvořte instanční objekt.

4. Přejmenujte název počítače počítače.

5. Agenta připojeného počítače znovu zaregistrujte se servery s podporou ARC. Spusťte `azcmagent` Nástroj s parametrem [Connect](manage-agent.md#connect) a dokončete tento krok.

6. Znovu nasaďte rozšíření virtuálních počítačů, které byly původně nasazeny do počítače ze serverů s podporou ARC. Pokud jste nasadili agenta Azure Monitor pro virtuální počítače (Insights) nebo agenta Log Analytics pomocí zásad Azure, agenti se znovu nasadí po dalším [zkušebním cyklu](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="upgrading-agent"></a>Upgrade agenta

Agent připojeného počítače Azure se pravidelně aktualizuje a řeší opravy chyb, vylepšení stability a nové funkce. [Azure Advisor](../../advisor/advisor-overview.md) identifikuje prostředky, které nepoužívají nejnovější verzi agenta Machine agent, a doporučuje, abyste provedli upgrade na nejnovější verzi. Zobrazí se vám upozornění, když vyberete server s podporou ARC tím, že zobrazíte banner na stránce **Přehled** nebo při přístupu ke službě Advisor prostřednictvím Azure Portal.

Agenta připojeného počítače Azure pro Windows a Linux se dá upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na vašich požadavcích.

Následující tabulka popisuje metody podporované pro provedení upgradu agenta.

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

Agenta je možné upgradovat podle různých metod, které podporují proces správy aktualizací softwaru. Mimo získání z Microsoft Update můžete stáhnout a spustit ručně z příkazového řádku, ze skriptu nebo jiného řešení automatizace nebo z Průvodce uživatelským rozhraním spuštěním `AzureConnectedMachine.msi` .

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

Nástroj Azcmagent (Azcmagent.exe) se používá ke konfiguraci serverů s povoleným obloukem Azure ARC připojeného agenta počítače během instalace nebo po instalaci změnit počáteční konfiguraci agenta. Azcmagent.exe poskytuje parametry příkazového řádku pro přizpůsobení agenta a zobrazení jeho stavu:

* **Připojení** – připojení počítače k Arc Azure

* **Odpojit** – odpojení počítače od Arc Azure

* **Zobrazit** – zobrazit stav agenta a jeho vlastnosti konfigurace (název skupiny prostředků, ID předplatného, verze atd.), která může pomáhat při řešení potíží s agentem. Zahrňte `-j` parametr pro výstup výsledků ve formátu JSON.

* **Protokoly** – vytvoří soubor. zip v aktuálním adresáři, který obsahuje protokoly, které vám pomůžou při řešení problémů.

* **Version (verze** ) zobrazuje verzi agenta připojeného počítače.

* **-h nebo--help** -zobrazí dostupné parametry příkazového řádku.

    Pokud například chcete zobrazit podrobnou nápovědu k parametru **připojení** , zadejte `azcmagent connect -h` . 

* **-v nebo--verbose** – Povolit podrobné protokolování

Můžete provést **připojení** a **odpojení** ručně během interaktivního přihlášení nebo automatizovat pomocí stejného instančního objektu, který jste použili k připojování více agentů nebo k Microsoft Identity Platform [Access tokenu](../../active-directory/develop/access-tokens.md). Pokud jste nepoužívali instanční objekt k registraci počítače se servery s podporou ARC Azure, přečtěte si následující [článek](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a vytvořte instanční objekt.

>[!NOTE]
>Aby bylo možné spustit **azcmagent**, musíte mít oprávnění *root* Access pro počítače se systémem Linux.

### <a name="connect"></a>Připojit

Tento parametr určuje prostředek v Azure Resource Manager, který představuje počítač vytvořený v Azure. Prostředek je v předplatném a v zadané skupině prostředků a data o počítači se ukládají v oblasti Azure určené `--location` nastavením. Výchozí název prostředku je název hostitele počítače, pokud není zadaný.

Certifikát, který odpovídá identitě přiřazené systémem počítače, se pak stáhne a uloží místně. Po dokončení tohoto kroku se připojí počítač Azure Metadata Service a Agent konfigurace hosta začne synchronizovat se servery s podporou ARC Azure.

Pokud se chcete připojit pomocí instančního objektu, spusťte následující příkaz:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Pokud se chcete připojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Pokud se chcete připojit pomocí přihlášených přihlašovacích údajů se zvýšenými oprávněními (Interactive), spusťte následující příkaz:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Odpojit

Tento parametr určuje prostředek v Azure Resource Manager, který představuje počítač odstraněný v Azure. Neodebere agenta z počítače, odinstalujete agenta samostatně. Pokud je počítač odpojený, pokud ho chcete znovu zaregistrovat u serverů s podporou ARC Azure, použijte proto, `azcmagent connect` aby byl pro něj vytvořen nový prostředek v Azure.

> [!NOTE]
> Pokud jste nasadili jedno nebo více rozšíření virtuálních počítačů Azure na server s podporou ARC a odstraníte jeho registraci v Azure, budou tato rozšíření pořád nainstalovaná. Je důležité si uvědomit, že v závislosti na nainstalovaném rozšíření je aktivně prováděn jeho funkce. Počítače, které mají být vyřazené nebo už spravované servery s podporou ARC, by se nejdřív měly před odebráním registrace z Azure odebrat.

Pokud se chcete odpojit pomocí instančního objektu, spusťte následující příkaz:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Pokud chcete odpojit pomocí přístupového tokenu, spusťte následující příkaz:

`azcmagent disconnect --access-token <accessToken>`

Pokud se chcete odpojit od přihlašovacích údajů se zvýšenými oprávněními (interaktivní), spusťte následující příkaz:

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Odebrat agenta

Proveďte jednu z následujících metod k odinstalaci agenta připojeného počítače s Windows nebo Linux z počítače. Odebrání agenta zruší registraci počítače u serverů s povoleným ARC nebo odebrat nainstalované rozšíření virtuálních počítačů Azure. Zrušte registraci počítače a odeberte nainstalovaná rozšíření virtuálních počítačů, když už nepotřebujete spravovat počítač v Azure a tyto kroky je potřeba provést před odinstalací agenta.

### <a name="windows-agent"></a>Agent Windows

Obě následující metody odeberou agenta, ale neodstraňují složku *C:\Program Files\AzureConnectedMachineAgent* v počítači.

#### <a name="uninstall-from-control-panel"></a>Odinstalace z ovládacích panelů

1. Chcete-li odinstalovat agenta systému Windows z počítače, postupujte takto:

    a. Přihlaste se k počítači pomocí účtu, který má oprávnění správce.  
    b. V **Ovládacích panelech** vyberte **programy a funkce**.  
    c. V **programech a funkcích** vyberte **Azure Connected Machine agent**, vyberte **odinstalovat** a pak vyberte **Ano**.  

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

Pokud plánujete ukončit správu počítače s podpůrnými službami v Azure, proveďte následující kroky, abyste zrušili registraci počítače u serverů s podporou ARC. Tyto kroky můžete provést buď před, nebo po odebrání agenta připojeného počítače z počítače.

1. Otevřete servery s podporou ARC Azure pomocí přechodu na [Azure Portal](https://aka.ms/hybridmachineportal).

2. Vyberte počítač v seznamu, vyberte tři tečky (**...**) a pak vyberte **Odstranit**.

## <a name="update-or-remove-proxy-settings"></a>Aktualizovat nebo odebrat nastavení proxy serveru

Chcete-li nakonfigurovat agenta ke komunikaci se službou prostřednictvím proxy server nebo tuto konfiguraci po nasazení odebrat, nebo použijte k dokončení této úlohy jednu z následujících metod.

> [!NOTE]
> Servery s podporou ARC nepodporují použití [Log Analytics brány](../../azure-monitor/agents/gateway.md) jako proxy pro agenta připojeného počítače.
>

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

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/vm/vminsights-enable-policy.md)a mnohem víc.

* Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).