---
title: Nasazení Windows Hybrid Runbook Worker v Azure Automation
description: V tomto článku se dozvíte, jak nasadit Hybrid Runbook Worker, které můžete použít ke spouštění Runbooků v počítačích se systémem Windows v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: a03d14fa272f5f86af1caf0ce9537bbb186d13cc
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204513"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker Windows

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo na počítači, který je hostitelem role a k prostředkům v prostředí za účelem správy těchto místních prostředků. Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více určených počítačů. Tento článek popisuje, jak nasadit Hybrid Runbook Worker v počítači s Windows, jak odebrat pracovní proces a jak odebrat skupinu Hybrid Runbook Worker.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující.

### <a name="a-log-analytics-workspace"></a>Pracovní prostor Log Analytics

Role Hybrid Runbook Worker závisí na pracovním prostoru Azure Monitor Log Analytics k instalaci a konfiguraci role. Můžete ji vytvořit prostřednictvím [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), prostřednictvím [PowerShellu](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)nebo v [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Pokud nemáte pracovní prostor Azure Monitor Log Analytics, před vytvořením pracovního prostoru si přečtěte téma [Průvodce návrhem protokolu Azure monitor](../azure-monitor/platform/design-logs-deployment.md) .

Pokud máte pracovní prostor, ale není propojený s vaším účtem Automation, umožňuje funkce automatizace přidat funkce pro Azure Automation, včetně podpory Hybrid Runbook Worker. Pokud povolíte jednu z Azure Automation funkcí v pracovním prostoru Log Analytics, konkrétně [Update Management](update-management/update-mgmt-overview.md) nebo [Change Tracking a inventáře](change-tracking/overview.md), automaticky se do počítače agenta přiřadí komponenty pracovního procesu.

> [!NOTE]
> Když povolíte funkci Update Management nebo Change Tracking a inventáře, Azure Automation podporuje jenom určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md). Než povolíte některou z funkcí, přečtěte si informace o [cenách Azure](https://azure.microsoft.com/pricing/details/automation/) pro Azure Automation.

   Pokud chcete přidat funkci Update Management do svého pracovního prostoru, spusťte následující rutinu PowerShellu:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Pokud chcete do svého pracovního prostoru přidat funkci Change Tracking a inventáře, spusťte následující rutinu PowerShellu:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agent Log Analytics

Role Hybrid Runbook Worker vyžaduje, aby byl [Agent pro Log Analytics](../azure-monitor/platform/log-analytics-agent.md) podporovaný operačním systémem Windows.

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

Pro Hybrid Runbook Worker Windows jsou oficiálně podporované následující verze operačního systému Windows:

* Windows Server 2019
* Windows Server 2016, verze 1709 a 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (včetně více relací) a pro
* Windows 8 Enterprise a pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimální požadavky

Minimální požadavky pro Windows Hybrid Runbook Worker jsou:

* Windows PowerShell 5,1 nebo novější ([Stáhnout WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 nebo novější
* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="network-configuration"></a>Konfigurace sítě

Další požadavky na síť pro Hybrid Runbook Worker najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Přidání počítače do skupiny Hybrid Runbook Worker

Pracovní počítač můžete přidat do skupiny Hybrid Runbook Worker v účtu Automation. Všimněte si, že je potřeba podporovat Runbooky Automation, pokud používáte stejný účet pro funkci Azure Automation i pro členství v Hybrid Runbook Worker skupině. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

>[!NOTE]
>Povolením Azure Automation [Update Management](update-management/update-mgmt-overview.md) automaticky nakonfigurujeme počítač s Windows, který je připojený ke svému pracovnímu prostoru Log Analytics, jako Hybrid Runbook Worker pro podporu správy aktualizací operačního systému. Tento pracovní proces ale není zaregistrovaný u žádné Hybrid Runbook Worker skupiny, které už jsou ve vašem účtu Automation definované.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Povolování počítačů pro správu pomocí konfigurace stavu Azure Automation

Informace o povolování počítačů pro správu pomocí konfigurace stavu Azure Automation najdete v tématu [Povolení počítačů pro správu pomocí konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

> [!NOTE]
> Chcete-li spravovat konfiguraci počítačů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné přidat počítače jako uzly DSC.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Možnosti instalace Windows Hybrid Runbook Worker

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker systému Windows, můžete použít jednu z následujících metod.

* Pro virtuální počítače Azure nainstalujte agenta Log Analytics pro Windows pomocí [rozšíření virtuálního počítače pro Windows](../virtual-machines/extensions/oms-windows.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics pomocí šablony Azure Resource Manager nebo PowerShellu. Po instalaci agenta je možné virtuální počítač přidat do skupiny Hybrid Runbook Worker v účtu Automation.

* V případě virtuálních počítačů mimo Azure nainstalujte agenta Log Analytics pro Windows pomocí možností nasazení popsaných v tématu [připojení počítačů se systémem Windows k Azure monitor](../azure-monitor/platform/agent-windows.md) článku. Tento postup můžete opakovat, pokud chcete přidat více pracovních procesů do vašeho prostředí. Po instalaci agenta je možné virtuální počítače přidat do skupiny Hybrid Runbook Worker v účtu Automation.

* K úplnému [automatizaci](#automated-deployment) procesu konfigurace jednoho nebo více počítačů se systémem Windows použijte poskytnutý skript prostředí PowerShell. Toto je doporučená metoda pro počítače ve vašem datovém centru nebo jiném cloudovém prostředí.

## <a name="automated-deployment"></a>Automatizované nasazení

V cílovém počítači pomocí následujících kroků Automatizujte instalaci a konfiguraci role Windows Hybrid Worker pomocí **New-OnPremiseHybridWorker.ps1**skriptu PowerShellu. Skript provede následující kroky:

* Nainstaluje potřebné moduly.
* Přihlaste se pomocí účtu Azure.
* Ověřuje existenci zadané skupiny prostředků a účtu Automation.
* Vytvoří odkazy na atributy účtu Automation.
* Vytvoří pracovní prostor Azure Monitor Log Analytics, pokud není zadaný.
* Povolení řešení Azure Automation v pracovním prostoru
* Stažení a instalace agenta Log Analytics pro Windows
* Zaregistrovat počítač jako Hybrid Runbook Worker

### <a name="step-1---download-the-powershell-script"></a>Krok 1 – stažení skriptu PowerShellu

Stáhněte si skript **New-OnPremiseHybridWorker.ps1** z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Po stažení skriptu ho zkopírujte nebo spusťte na cílovém počítači. Skript **New-OnPremiseHybridWorker.ps1** používá parametry popsané níže během provádění.

| Parametr | Status | Popis |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Povinné | Název skupiny prostředků, která je přidružená k vašemu účtu Automation. |
| `AutomationAccountName` | Povinné | Název vašeho účtu Automation.
| `Credential` | Nepovinné | Přihlašovací údaje, které se mají použít při přihlašování do prostředí Azure. |
| `HybridGroupName` | Povinné | Název skupiny Hybrid Runbook Worker, kterou zadáte jako cíl pro Runbooky, které podporují tento scénář. |
| `OMSResourceGroupName` | Nepovinné | Název skupiny prostředků pro pracovní prostor Log Analytics. Pokud není tato skupina prostředků zadaná, použije se hodnota `AAResourceGroupName` . |
| `SubscriptionID` | Povinné | Identifikátor předplatného Azure přidruženého k vašemu účtu Automation. |
| `TenantID` | Nepovinné | Identifikátor organizace tenanta přidružené k vašemu účtu Automation. |
| `WorkspaceName` | Nepovinné | Název Log Analytics pracovního prostoru. Pokud nemáte pracovní prostor Log Analytics, skript ho vytvoří a nakonfiguruje. |

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Krok 2 – otevření prostředí příkazového řádku Windows PowerShellu

V **nabídce Start** klikněte na **Start**, zadejte **PowerShell**, klikněte pravým tlačítkem na **Windows PowerShell**a pak klikněte na **Spustit jako správce**.

### <a name="step-3---run-the-powershell-script"></a>Krok 3 – spuštění skriptu PowerShellu

V prostředí příkazového řádku PowerShellu přejděte do složky, která obsahuje skript, který jste stáhli. Změňte hodnoty parametrů `AutomationAccountName` ,,,, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` a `WorkspaceName` . Potom spusťte skript.

Po spuštění skriptu budete vyzváni k ověření pomocí Azure. Musíte se přihlásit pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

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

### <a name="step-4---install-nuget"></a>Krok 4 – instalace NuGetu

Budete vyzváni k vyjádření souhlasu s instalací NuGet a k ověření pomocí přihlašovacích údajů Azure. Pokud nemáte nejnovější verzi NuGet, můžete ji stáhnout z [dostupných verzí distribuce NuGet](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Krok 5 – ověření nasazení

Po dokončení skriptu se na stránce Hybrid Worker skupiny v účtu Automation zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce Hybrid Worker skupiny a kliknout na dlaždici **hybridní pracovní procesy** . Na stránce hybridní pracovní procesy uvidíte všechny členy uvedené skupiny.

## <a name="manual-deployment"></a>Ruční nasazení

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker systému Windows, proveďte následující kroky.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Krok 1 – ověření, zda agent hlásí do pracovního prostoru

Agent Log Analytics pro systém Windows připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do vašeho počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřebné pro Hybrid Runbook Worker.

Jakmile se agent úspěšně připojí k pracovnímu prostoru Log Analytics po několika minutách, můžete spustit následující dotaz, který ověří, jestli odesílá data prezenčního signálu do pracovního prostoru.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro daný počítač, což znamená, že je připojený a oznamuje službě zprávy. Ve výchozím nastavení každý Agent přepošle záznam prezenčního signálu do přiřazeného pracovního prostoru. Pomocí následujících kroků dokončete instalaci a instalaci agenta.

1. Povolením funkce přidejte počítač agenta. Informace o Update Management a virtuálních počítačích Azure najdete v tématu [povolení Update Management z účtu Automation](update-management/update-mgmt-enable-automation-account.md), [Povolení Update Management procházením Azure Portal](update-management/update-mgmt-enable-portal.md), [povolením Update Management z Runbooku](update-management/update-mgmt-enable-runbook.md)nebo [povolením Update Management z virtuálního počítače Azure](update-management/update-mgmt-enable-vm.md). Informace o Change Tracking a virtuálních počítačích Azure najdete v tématu [Povolení virtuálních počítačů Azure](change-tracking/enable-from-automation-account.md#enable-azure-vms)a pro virtuální počítače mimo Azure najdete v tématu [Povolení počítačů v pracovním prostoru](change-tracking/enable-from-automation-account.md#enable-machines-in-the-workspace).

2. Chcete-li ověřit verzi Hybrid Runbook Worker, přejděte na `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` podsložku **verze** a poznamenejte si ji.

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>Krok 2 – instalace prostředí Runbooku a připojení k Azure Automation

Když nakonfigurujete agenta tak, aby nahlásil do Log Analytics pracovního prostoru, funkce Azure Automation vloží `HybridRegistration` modul PowerShellu, který obsahuje `Add-HybridRunbookWorker` rutinu. Pomocí této rutiny nainstalujete do počítače prostředí Runbooku a zaregistrujete ho do Azure Automation.

Otevřete relaci PowerShellu v režimu správce a spuštěním následujících příkazů Importujte modul.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Nyní spusťte `Add-HybridRunbookWorker` rutinu pomocí následující syntaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

Můžete získat informace požadované pro parametry `Url` a `Key` ze stránky **klíče** ve vašem účtu Automation. V části **Nastavení účtu** na levé straně stránky vyberte **klíče** .

![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pro `Url` parametr Zkopírujte hodnotu pro **URL**.

* Pro `Key` parametr Zkopírujte hodnotu **primárního přístupového klíče**.

* Pro `GroupName` parametr použijte název skupiny Hybrid Runbook Worker. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, přidá se.

* V případě potřeby nastavte `Verbose` parametr pro příjem podrobností o instalaci.

### <a name="step-3----install-powershell-modules"></a>Krok 3 – Instalace modulů PowerShellu

Runbooky můžou používat jakékoli aktivity a rutiny definované v modulech nainstalovaných ve vašem Azure Automationovém prostředí. Protože tyto moduly nejsou automaticky nasazené do místních počítačů, musíte je nainstalovat ručně. Výjimkou je modul Azure. Tento modul se instaluje ve výchozím nastavení a poskytuje přístup k rutinám pro všechny služby a aktivity Azure pro Azure Automation.

Vzhledem k tomu, že primárním účelem Hybrid Runbook Worker je spravovat místní prostředky, pravděpodobně budete muset nainstalovat moduly, které tyto prostředky podporují, zejména `PowerShellGet` modul. Informace o instalaci modulů prostředí Windows PowerShell najdete v tématu [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Moduly, které jsou nainstalovány, musí být v umístění, na `PSModulePath` které odkazuje proměnná prostředí, aby je hybridní pracovní proces mohl automaticky importovat. Další informace najdete v tématu [Instalace modulů v PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Odebrání Hybrid Runbook Worker z místního počítače s Windows

1. V Azure Portal přejdete do svého účtu Automation.

2. V části **Nastavení účtu**vyberte **klíče** a poznamenejte si hodnoty **adresy URL** a **primárního přístupového klíče**.

3. Otevřete relaci PowerShellu v režimu správce a spusťte následující příkaz s hodnotami adresy URL a primárního přístupového klíče. Použijte `Verbose` parametr pro podrobný protokol procesu odebrání. K odebrání zastaralých počítačů ze skupiny Hybrid Worker Použijte volitelný `machineName` parametr.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Odebrání skupiny Hybrid Worker

Chcete-li odebrat skupinu Hybrid Runbook Worker, musíte nejprve odebrat Hybrid Runbook Worker z každého počítače, který je členem skupiny. Pak odeberte skupinu pomocí následujících kroků:

1. Otevřete účet Automation v Azure Portal.

2. V části **Automatizace procesu**vyberte **skupiny hybridních pracovních procesů** . Vyberte skupinu, kterou chcete odstranit. Zobrazí se stránka Vlastnosti této skupiny.

   ![Stránka Vlastnosti](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na stránce vlastnosti vybrané skupiny vyberte **Odstranit**. Zobrazí se zpráva s výzvou k potvrzení této akce. Pokud jste si jisti, že chcete pokračovat, vyberte **Ano** .

   ![Potvrzovací zpráva](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dokončení tohoto procesu může trvat několik sekund. Průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Informace o tom, jak řešit potíže s procesy Hybrid Runbook Worker, najdete v tématu [řešení potíží s Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
