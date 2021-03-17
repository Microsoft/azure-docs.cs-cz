---
title: Nasazení Windows Hybrid Runbook Worker v Azure Automation
description: V tomto článku se dozvíte, jak nasadit Hybrid Runbook Worker, které můžete použít ke spouštění Runbooků v počítačích se systémem Windows v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: f6858c7350e6c72a096b2f2bd5f4a4ff606bf023
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651353"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker Windows

Pomocí funkce User Hybrid Runbook Worker Azure Automation můžete spouštět Runbooky přímo na počítačích Azure nebo mimo Azure, včetně serverů zaregistrovaných u [serverů s podporou ARC Azure](../azure-arc/servers/overview.md). Z počítače nebo serveru, který je hostitelem role, můžete spouštět Runbooky přímo proti němu a k prostředkům v prostředí pro správu těchto místních prostředků.

Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více určených počítačů. Tento článek popisuje, jak nasadit Hybrid Runbook Worker uživatelů na počítač s Windows, jak odebrat pracovní proces a jak odebrat skupinu Hybrid Runbook Worker.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující.

### <a name="a-log-analytics-workspace"></a>Pracovní prostor Log Analytics

Role Hybrid Runbook Worker závisí na pracovním prostoru Azure Monitor Log Analytics k instalaci a konfiguraci role. Můžete ji vytvořit prostřednictvím [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), prostřednictvím [PowerShellu](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)nebo v [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Pokud nemáte pracovní prostor Azure Monitor Log Analytics, před vytvořením pracovního prostoru si přečtěte téma [Průvodce návrhem protokolu Azure monitor](../azure-monitor/logs/design-logs-deployment.md) .

### <a name="log-analytics-agent"></a>Agent Log Analytics

Role Hybrid Runbook Worker vyžaduje, aby byl [Agent pro Log Analytics](../azure-monitor/agents/log-analytics-agent.md) podporovaný operačním systémem Windows. U serverů nebo počítačů hostovaných mimo Azure můžete agenta Log Analytics nainstalovat pomocí [serverů s podporou ARC Azure](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

Funkce Hybrid Runbook Worker podporuje následující operační systémy:

* Windows Server 2019 (včetně jádra serveru)
* Windows Server 2016, verze 1709 a 1803 (kromě jádra serveru)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (včetně více relací) a pro
* Windows 8 Enterprise a pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimální požadavky

Minimální požadavky pro systém Windows a uživatelské Hybrid Runbook Worker jsou:

* Windows PowerShell 5,1 ([Stáhnout WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core se nepodporuje.
* .NET Framework 4.6.2 nebo novější
* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="network-configuration"></a>Konfigurace sítě

Požadavky na síť pro Hybrid Runbook Worker najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Přidání počítače do skupiny Hybrid Runbook Worker

Pracovní počítač můžete přidat do skupiny Hybrid Runbook Worker v jednom z vašich účtů Automation. Pro počítače, které hostují systém Hybrid Runbook Worker spravované pomocí Update Management, je lze přidat do skupiny Hybrid Runbook Worker. Je ale nutné použít stejný účet Automation pro Update Management a členství v Hybrid Runbook Worker skupině.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automaticky nainstaluje systémovou Hybrid Runbook Worker na počítač s Azure nebo mimo Azure, který je povolený pro Update Management. Tento pracovní proces ale není zaregistrovaný u žádné skupiny Hybrid Runbook Worker ve vašem účtu Automation. Pokud chcete spouštět Runbooky na těchto počítačích, musíte je přidat do skupiny Hybrid Runbook Worker. Pokud chcete přidat do skupiny, postupujte podle kroku 6 v části [Ruční nasazení](#manual-deployment) .

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Povolit pro správu s konfigurací stavu Azure Automation

Informace o povolování počítačů pro správu pomocí konfigurace stavu Azure Automation najdete v tématu [Povolení počítačů pro správu pomocí konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

> [!NOTE]
> Chcete-li spravovat konfiguraci počítačů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné přidat počítače jako uzly DSC.

## <a name="installation-options"></a>Možnosti instalace

Pokud chcete nainstalovat a nakonfigurovat uživatelské Hybrid Runbook Worker Windows, můžete použít jednu z následujících metod.

* K úplnému [automatizaci](#automated-deployment) procesu konfigurace jednoho nebo více počítačů se systémem Windows použijte poskytnutý skript prostředí PowerShell. Toto je doporučená metoda pro počítače ve vašem datovém centru nebo jiném cloudovém prostředí.
* Ručně importujte řešení automatizace, nainstalujte agenta Log Analytics pro Windows a na počítači nakonfigurujte roli pracovního procesu.

## <a name="automated-deployment"></a>Automatizované nasazení

Metoda automatizovaného nasazení používá skript PowerShellu **New-OnPremiseHybridWorker.ps1** k automatizaci a konfiguraci role Windows Hybrid Runbook Worker. Provede následující:

* Nainstaluje potřebné moduly.
* Přihlaste se pomocí účtu Azure.
* Ověřuje existenci zadané skupiny prostředků a účtu Automation.
* Vytvoří odkazy na atributy účtu Automation.
* Vytvoří pracovní prostor Azure Monitor Log Analytics, pokud není zadaný.
* Povolení řešení Azure Automation v pracovním prostoru
* Stažení a instalace agenta Log Analytics pro Windows
* Zaregistrovat počítač jako Hybrid Runbook Worker

Provedením následujících kroků nainstalujete roli na počítač s Windows pomocí skriptu.

1. Stáhněte si skript **New-OnPremiseHybridWorker.ps1** z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Po stažení skriptu ho zkopírujte nebo spusťte na cílovém počítači. **New-OnPremiseHybridWorker.ps1** skript při spuštění používá následující parametry.

    | Parametr | Status | Popis |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Povinné | Název skupiny prostředků, která je přidružená k vašemu účtu Automation. |
    | `AutomationAccountName` | Povinné | Název vašeho účtu Automation.
    | `Credential` | Volitelné | Přihlašovací údaje, které se mají použít při přihlašování do prostředí Azure. |
    | `HybridGroupName` | Povinné | Název skupiny Hybrid Runbook Worker, kterou zadáte jako cíl pro Runbooky, které podporují tento scénář. |
    | `OMSResourceGroupName` | Volitelné | Název skupiny prostředků pro pracovní prostor Log Analytics. Pokud není tato skupina prostředků zadaná, použije se hodnota `AAResourceGroupName` . |
    | `SubscriptionID` | Povinné | Identifikátor předplatného Azure přidruženého k vašemu účtu Automation. |
    | `TenantID` | Volitelné | Identifikátor organizace tenanta přidružené k vašemu účtu Automation. |
    | `WorkspaceName` | Volitelné | Název Log Analytics pracovního prostoru. Pokud nemáte pracovní prostor Log Analytics, skript ho vytvoří a nakonfiguruje. |

2. Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními 64.

3. Z příkazového řádku PowerShellu přejděte do složky, která obsahuje skript, který jste stáhli. Změňte hodnoty parametrů `AutomationAccountName` ,,,, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` a `WorkspaceName` . Potom spusťte skript.

    Po spuštění skriptu budete vyzváni k ověření pomocí Azure. Musíte se přihlásit pomocí účtu, který je členem role **správců předplatného** a spolusprávcem předplatného.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. Budete vyzváni k vyjádření souhlasu s instalací NuGet a k ověření pomocí přihlašovacích údajů Azure. Pokud nemáte nejnovější verzi NuGet, můžete ji stáhnout z [dostupných verzí distribuce NuGet](https://www.nuget.org/downloads).

5. Ověřte nasazení po dokončení skriptu. Na stránce **Hybrid Runbook Worker skupiny** ve vašem účtu Automation na kartě **Skupina Hybrid Runbook Worker uživatele** se zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce. v nabídce vlevo zvolte **hybridní pracovní procesy** . Na stránce **hybridní pracovní procesy** uvidíte všechny členy uvedené skupiny.

## <a name="manual-deployment"></a>Ruční nasazení

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker systému Windows, proveďte následující kroky.

1. Povolte řešení Azure Automation v pracovním prostoru Log Analytics spuštěním následujícího příkazu v příkazovém řádku PowerShellu se zvýšenými oprávněními nebo v Cloud Shell v [Azure Portal](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Nasaďte agenta Log Analytics do cílového počítače.

    * Pro virtuální počítače Azure nainstalujte agenta Log Analytics pro Windows pomocí [rozšíření virtuálního počítače pro Windows](../virtual-machines/extensions/oms-windows.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Pomocí šablony Azure Resource Manager, PowerShellu nebo Azure Policy můžete přiřadit předdefinované zásady [nasazení Log Analytics agenta pro virtuální počítače se *systémem* *Linux* nebo Windows](../governance/policy/samples/built-in-policies.md#monitoring) . Po instalaci agenta je možné počítač přidat do skupiny Hybrid Runbook Worker v účtu Automation.
    
    * U počítačů mimo Azure můžete agenta Log Analytics nainstalovat pomocí [serverů s podporou ARC Azure](../azure-arc/servers/overview.md). Servery s podporou ARC podporují nasazení Log Analytics agenta pomocí následujících metod:
    
        - Pomocí architektury rozšíření virtuálních počítačů.
        
            Tato funkce na serverech s podporou ARC Azure umožňuje nasadit rozšíření virtuálního počítače Log Analytics agenta do jiného serveru než Azure s Windows nebo Linux. Rozšíření virtuálních počítačů je možné spravovat pomocí následujících metod na hybridních počítačích nebo serverech spravovaných servery s podporou ARC:
        
            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Rozhraní příkazového [řádku Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Šablony Azure správce prostředků](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Použití Azure Policy.
        
            Pomocí tohoto přístupu použijete Azure Policy [nasadit Log Analytics agenta do systému Linux nebo integrované zásady Windows Azure ARC](../governance/policy/samples/built-in-policies.md#monitoring) pro audit, pokud je na serveru s povoleným Arc agent Log Analytics nainstalován. Pokud není agent nainstalovaný, automaticky ho nasadí pomocí úlohy nápravy. Případně, pokud plánujete monitorovat počítače pomocí Azure Monitor pro virtuální počítače, místo toho použijte k instalaci a konfiguraci agenta Log Analytics [možnost povolit Azure monitor pro virtuální počítače](../governance/policy/samples/built-in-initiatives.md#monitoring) .

    K instalaci agenta Log Analytics pro Windows nebo Linux doporučujeme použít Azure Policy.

3. Ověřit, jestli je agent vytváření sestav do pracovního prostoru

    Agent Log Analytics pro systém Windows připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do vašeho počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřebné pro Hybrid Runbook Worker.

    Jakmile se agent úspěšně připojí k pracovnímu prostoru Log Analytics po několika minutách, můžete spustit následující dotaz, který ověří, jestli odesílá data prezenčního signálu do pracovního prostoru.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro daný počítač, což znamená, že je připojený a oznamuje službě zprávy. Ve výchozím nastavení každý Agent přepošle záznam prezenčního signálu do přiřazeného pracovního prostoru. Pomocí následujících kroků dokončete instalaci a instalaci agenta.

4. Potvrďte verzi Hybrid Runbook Worker na počítači, který je hostitelem agenta Log Analytics, přejděte na `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` podsložku **verze** a poznamenejte si ji. Tato složka se zobrazí na počítači několik minut poté, co je řešení povoleno v pracovním prostoru.

5. Nainstalujte prostředí Runbooku a připojte se k Azure Automation. Když nakonfigurujete agenta tak, aby nahlásil do Log Analyticsho pracovního prostoru a importoval řešení **Automatizace** , řešení bude nabízet `HybridRegistration` modul PowerShellu. Tento modul obsahuje `Add-HybridRunbookWorker` rutinu. Pomocí této rutiny nainstalujete do počítače prostředí Runbooku a zaregistrujete ho do Azure Automation.

    Otevřete relaci PowerShellu v režimu správce a spuštěním následujících příkazů Importujte modul.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Spusťte `Add-HybridRunbookWorker` rutinu, která určuje hodnoty parametrů `Url` , `Key` a `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Můžete získat informace požadované pro parametry `Url` a `Key` ze stránky **klíče** ve vašem účtu Automation. V části **Nastavení účtu** na levé straně stránky vyberte **klíče** .

    ![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Pro `Url` parametr Zkopírujte hodnotu pro **URL**.

    * Pro `Key` parametr Zkopírujte hodnotu **primárního přístupového klíče**.

    * Pro `GroupName` parametr použijte název skupiny Hybrid Runbook Worker. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, přidá se.

    * V případě potřeby nastavte `Verbose` parametr pro příjem podrobností o instalaci.

7. Po dokončení příkazu ověřte nasazení. Na stránce **Hybrid Runbook Worker skupiny** ve vašem účtu Automation na kartě **Skupina hybridních pracovních procesů Runbooku uživatele** se zobrazí nová nebo existující skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce. v nabídce vlevo zvolte **hybridní pracovní procesy**. Na stránce **hybridní pracovní procesy** uvidíte všechny členy uvedené skupiny.

## <a name="install-powershell-modules"></a>Nainstalovat moduly PowerShellu

Runbooky můžou používat jakékoli aktivity a rutiny definované v modulech nainstalovaných ve vašem Azure Automationovém prostředí. Protože tyto moduly nejsou automaticky nasazené do místních počítačů, musíte je nainstalovat ručně. Výjimkou je modul Azure. Tento modul se instaluje ve výchozím nastavení a poskytuje přístup k rutinám pro všechny služby a aktivity Azure pro Azure Automation.

Vzhledem k tomu, že primárním účelem Hybrid Runbook Worker je spravovat místní prostředky, pravděpodobně budete muset nainstalovat moduly, které tyto prostředky podporují, zejména `PowerShellGet` modul. Informace o instalaci modulů prostředí Windows PowerShell najdete v tématu [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Moduly, které jsou nainstalovány, musí být v umístění, na `PSModulePath` které odkazuje proměnná prostředí, aby je hybridní pracovní proces mohl automaticky importovat. Další informace najdete v tématu [Instalace modulů v PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Odebrat Hybrid Runbook Worker

1. V Azure Portal přejdete do svého účtu Automation.

2. V části **Nastavení účtu** vyberte **klíče** a poznamenejte si hodnoty **adresy URL** a **primárního přístupového klíče**.

3. Otevřete relaci PowerShellu v režimu správce a spusťte následující příkaz s hodnotami adresy URL a primárního přístupového klíče. Použijte `Verbose` parametr pro podrobný protokol procesu odebrání. K odebrání zastaralých počítačů ze skupiny Hybrid Worker Použijte volitelný `machineName` parametr.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Odebrání skupiny Hybrid Worker

Chcete-li odebrat skupinu Hybrid Runbook Worker, musíte nejprve odebrat Hybrid Runbook Worker z každého počítače, který je členem skupiny. Pak odeberte skupinu pomocí následujících kroků:

1. Otevřete účet Automation v Azure Portal.

2. V části **Automatizace procesu** vyberte **skupiny hybridních pracovních procesů** . Vyberte skupinu, kterou chcete odstranit. Zobrazí se stránka Vlastnosti této skupiny.

   ![Stránka Vlastnosti](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stránce vlastnosti vybrané skupiny vyberte **Odstranit**. Zobrazí se zpráva s výzvou k potvrzení této akce. Pokud jste si jisti, že chcete pokračovat, vyberte **Ano** .

   ![Potvrzovací zpráva](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dokončení tohoto procesu může trvat několik sekund. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Informace o tom, jak řešit potíže s procesy Hybrid Runbook Worker, najdete v tématu [řešení potíží s Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
