---
title: Funkce Hybrid Runbook Worker služby Azure Automation ve Windows
description: Tento článek poskytuje informace o instalaci Hybrid Runbook Worker Azure Automation, které můžete použít ke spouštění Runbooků v počítačích se systémem Windows v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: a6d2e2d912f176a88dc993803d750e37cff1acb6
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443648"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker Windows

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodávány do jednoho nebo více určených počítačů. Tento článek popisuje, jak nasadit Hybrid Runbook Worker na počítači s Windows.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalace a konfigurace Windows Hybrid Runbook Worker

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker systému Windows, můžete použít jednu z následujících metod.

* Pro virtuální počítače Azure nainstalujete agenta Log Analytics pro Windows pomocí [rozšíření virtuálního počítače pro Windows](../virtual-machines/extensions/oms-windows.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics pomocí šablony Azure Resource Manager nebo PowerShellu. Po instalaci agenta je možné virtuální počítač přidat do skupiny Hybrid Runbook Worker v účtu Automation, a to podle **kroku 4** v části [Ruční nasazení](#manual-deployment) níže.

* K úplnému automatizaci procesu konfigurace počítače se systémem Windows použijte Runbook služby Automation. Toto je doporučená metoda pro počítače ve vašem datovém centru nebo v jiném cloudovém prostředí.

* Postupujte podle podrobných pokynů, jak ručně nainstalovat a nakonfigurovat roli Hybrid Runbook Worker na VIRTUÁLNÍm počítači mimo Azure.

> [!NOTE]
> Chcete-li spravovat konfiguraci serverů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné je přidat jako uzly DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimální požadavky pro Windows Hybrid Runbook Worker

Minimální požadavky pro Windows Hybrid Runbook Worker jsou:

* Windows Server 2012 nebo novější
* Windows PowerShell 5,1 nebo novější ([Stáhnout WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 nebo novější
* Dvě jádra
* 4 GB paměti RAM
* Port 443 (odchozí)

### <a name="network-configuration"></a>Konfigurace sítě

Další požadavky na síť pro Hybrid Runbook Worker najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Připojování serveru pro správu pomocí Automatizace DSC

Další informace o připojování serverů pro správu s DSC najdete v tématu [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md).
Pokud povolíte [řešení Update Management](../operations-management-suite/oms-solution-update-management.md), veškerý počítač s Windows, který je připojený k vašemu pracovnímu prostoru Log Analytics, se automaticky nakonfiguruje jako Hybrid Runbook Worker pro podporu runbooků obsažených v tomto řešení. Není ale zaregistrovaný u žádné Hybrid Workeré skupiny, které už jsou ve vašem účtu Automation definované. 

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Přidání počítače do skupiny Hybrid Runbook Worker

Počítač může být přidán do skupiny Hybrid Runbook Worker ve vašem účtu Automation, aby podporoval Runbooky Automation, pokud používáte stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

## <a name="automated-deployment"></a>Automatizované nasazení

V cílovém počítači proveďte následující kroky, které automatizují instalaci a konfiguraci role Windows Hybrid Worker.

### <a name="1-download-the-powershell-script"></a>1. Stáhněte si PowerShellový skript.

Stáhněte si skript New-OnPremiseHybridWorker. ps1 z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) přímo z počítače, na kterém běží role Hybrid Runbook Worker, nebo z jiného počítače ve vašem prostředí. Zkopírujte skript do pracovního procesu. Skript New-OnPremiseHybridWorker. ps1 vyžaduje během provádění následující parametry:

   * *AAResourceGroupName* (povinné): název skupiny prostředků, která je přidružená k vašemu účtu Automation.
   * *OMSResourceGroupName* (volitelné): název skupiny prostředků pro pracovní prostor Log Analytics. Pokud tato skupina prostředků není zadaná, použije se *AAResourceGroupName* .
   * *SubscriptionId* (povinné): ID předplatného Azure, ve kterém je váš účet Automation.
   * *TenantID* (volitelné): identifikátor organizace tenanta přidružené k vašemu účtu Automation.
   * *Pracovní prostor* (volitelné): Název Log Analytics pracovního prostoru. Pokud nemáte pracovní prostor Log Analytics, skript ho vytvoří a nakonfiguruje.
   * *AutomationAccountName* (povinné): název vašeho účtu Automation.
   * *HybridGroupName* (povinné): název skupiny Hybrid Runbook Worker, kterou zadáte jako cíl pro Runbooky, které podporují tento scénář.
   * *Přihlašovací údaje* (volitelné): přihlašovací údaje, které se mají použít při přihlašování do prostředí Azure.
  
   > [!NOTE]
   > Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
   >
   > Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Otevřete prostředí příkazového řádku Windows PowerShellu.

Spusťte **prostředí Windows PowerShell** z obrazovky **Start** v režimu správce.

### <a name="3-run-the-powershell-script"></a>3. Spusťte PowerShellový skript.

Z prostředí příkazového řádku PowerShellu přejděte do složky, která obsahuje skript, který jste stáhli. Změňte hodnoty parametrů *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*a *-Workspace*. Potom spusťte skript.

Po spuštění skriptu budete vyzváni k ověření pomocí Azure. Musíte se přihlásit pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. instalace NuGetu

Budete vyzváni k vyjádření souhlasu s instalací NuGet a k ověření pomocí přihlašovacích údajů Azure. Pokud nemáte nejnovější verzi NuGet, můžete ji získat z [dostupných verzí distribuce NuGet](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. Ověření nasazení

Po dokončení skriptu se na stránce **Hybrid Worker skupiny** zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce **Hybrid Worker skupiny** a vybrat dlaždici **hybridní pracovní procesy** . Na stránce **hybridní procesy** se zobrazí všichni členové skupiny v seznamu.

## <a name="manual-deployment"></a>Ruční nasazení

Na cílovém počítači proveďte první dva kroky pro vaše prostředí automatizace. Pak proveďte zbývající kroky pro každý pracovní počítač.

### <a name="1-create-a-log-analytics-workspace"></a>1. vytvoření pracovního prostoru Log Analytics

Pokud ještě nemáte pracovní prostor Log Analytics, před vytvořením pracovního prostoru si nejdřív Přečtěte [Průvodce návrhem protokolu Azure monitor](../azure-monitor/platform/design-logs-deployment.md) .

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Přidejte řešení pro automatizaci do pracovního prostoru Log Analytics.

Řešení automatizace přidává funkce pro Azure Automation, včetně podpory pro Hybrid Runbook Worker. Když přidáte řešení do svého pracovního prostoru Log Analytics, automaticky se do počítače agenta, který nainstalujete v dalším kroku, přiřadí součásti pracovního procesu.

Pokud chcete přidat řešení **Automatizace** do svého pracovního prostoru, spusťte následující rutinu prostředí PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Nainstalujte agenta Log Analytics pro Windows.

Agent Log Analytics pro systém Windows připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do svého počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřeba pro Hybrid Runbook Worker.

Chcete-li nainstalovat agenta v počítači, postupujte podle pokynů v tématu [připojení počítačů se systémem Windows k Azure monitor protokolů](../log-analytics/log-analytics-windows-agent.md). Tento postup můžete opakovat, pokud chcete do vašeho prostředí přidat více pracovních procesů.

Jakmile se agent úspěšně připojí k pracovnímu prostoru Log Analytics po několika minutách, můžete spustit následující dotaz, který ověří, jestli odesílá data prezenčního signálu do pracovního prostoru:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Ve výsledcích hledání byste měli zobrazit záznamy prezenčního signálu pro daný počítač, což znamená, že je připojený a oznamuje službě zprávy. Ve výchozím nastavení každý Agent přepošle záznam prezenčního signálu do přiřazeného pracovního prostoru. Můžete ověřit, že agent správně stáhl řešení automatizace, když má složku s názvem AzureAutomationFiles v adresáři C:\Program Files\Microsoft monitoring Agent\Agent. Pokud chcete potvrdit verzi Hybrid Runbook Worker, přejděte do složky C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation\ a poznamenejte si podsložku \\*verze* .

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Nainstalujte prostředí Runbooku a připojte se Azure Automation

Když nakonfigurujete agenta tak, aby nahlásil do Log Analytics pracovního prostoru, řešení automatizace vloží modul PowerShellu HybridRegistration, který obsahuje rutinu **Add-HybridRunbookWorker** . Pomocí této rutiny nainstalujete do počítače prostředí Runbooku a zaregistrujete ho do Azure Automation.

Otevřete relaci PowerShellu v režimu správce a spuštěním následujících příkazů Importujte modul.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Pak spusťte rutinu **Add-HybridRunbookWorker** pomocí následující syntaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informace požadované pro tuto rutinu můžete získat ze stránky Správa klíčů v Azure Portal. Tuto stránku otevřete výběrem možnosti **klíče** na stránce **Nastavení** v účtu Automation.

![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pro parametr *název_skupiny* použijte název skupiny Hybrid Runbook Worker. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, přidá se.
* Pro parametr *koncového bodu* použijte položku **Adresa URL** na stránce Správa klíčů.
* Jako parametr *tokenu* použijte položku **primárního přístupového klíče** na stránce Správa klíčů.

Pokud chcete získat podrobné informace o instalaci, použijte přepínač *-verbose* s příkazem **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. Instalace modulů PowerShellu

Runbooky můžou používat libovolnou z aktivit a rutin definovaných v modulech, které jsou nainstalované ve vašem Azure Automation prostředí. Tyto moduly nejsou automaticky nasazeny do místních počítačů, takže je musíte nainstalovat ručně. Výjimkou je modul Azure. Tento modul se instaluje ve výchozím nastavení a poskytuje přístup k rutinám pro všechny služby a aktivity Azure pro Azure Automation.

Vzhledem k tomu, že primárním účelem funkce Hybrid Runbook Worker je Správa místních prostředků, pravděpodobně budete muset nainstalovat moduly, které tyto prostředky podporují, zejména modul PowerShellGet. Informace o instalaci modulů prostředí Windows PowerShell najdete v tématu [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Moduly, které jsou nainstalovány, musí být v umístění, na které odkazuje proměnná prostředí PSModulePath, aby je hybridní pracovní proces mohl automaticky importovat. Další informace najdete v tématu [Instalace modulů v PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Pokyny, jak odebrat procesy Hybrid Runbook Worker, najdete v tématu [odebrání Azure Automationch procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Informace o řešení potíží s procesy Hybrid Runbook Worker najdete v tématu [řešení potíží s Windows Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)
* Další kroky, jak řešit problémy s Update Management, najdete v článku [Update Management: řešení potíží](troubleshoot/update-management.md).
