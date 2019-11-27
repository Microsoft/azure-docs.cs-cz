---
title: Funkce Hybrid Runbook Worker služby Azure Automation ve Windows
description: Tento článek poskytuje informace o instalaci Hybrid Runbook Worker Azure Automation, které můžete použít ke spouštění Runbooků v počítačích se systémem Windows v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd599fcfe403d64483e6b4db869b93b26f5db754
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480817"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker Windows

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodávány do jednoho nebo více určených počítačů. Tento článek popisuje, jak nainstalovat Hybrid Runbook Worker do počítače s Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalace Hybrid Runbook Worker Windows

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker systému Windows, můžete použít dvě metody. Doporučeným způsobem je použít sadu Automation Runbook k úplnému automatizaci procesu konfigurace počítače se systémem Windows. Druhá metoda je podle podrobných kroků, jak ručně nainstalovat a nakonfigurovat roli.

> [!NOTE]
> Chcete-li spravovat konfiguraci serverů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné je přidat jako uzly DSC.

Minimální požadavky pro Windows Hybrid Runbook Worker jsou:

* Windows Server 2012 nebo novější.
* Windows PowerShell 5,1 nebo novější ([Stáhnout WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 nebo novější.
* Dvě jádra.
* 4 GB paměti RAM.
* Port 443 (odchozí).

Další požadavky na síť pro Hybrid Runbook Worker najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

Další informace o připojování serverů pro správu s DSC najdete v tématu [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md).
Pokud povolíte [řešení Update Management](../operations-management-suite/oms-solution-update-management.md), veškerý počítač s Windows, který je připojený k vašemu pracovnímu prostoru Azure Log Analytics, se automaticky nakonfiguruje jako Hybrid Runbook Worker pro podporu runbooků obsažených v tomto řešení. Není ale zaregistrovaný u žádné Hybrid Workeré skupiny, které už jsou ve vašem účtu Automation definované. 

Počítač může být přidán do skupiny Hybrid Runbook Worker ve vašem účtu Automation, aby podporoval Runbooky Automation, pokud používáte stejný účet pro řešení i pro členství v Hybrid Runbook Worker skupině. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

### <a name="automated-deployment"></a>Automatizované nasazení

K automatizaci instalace a konfigurace role Windows Hybrid Worker postupujte podle následujících kroků:

1. Stáhněte si skript New-OnPremiseHybridWorker. ps1 z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) přímo z počítače, na kterém běží role Hybrid Runbook Worker, nebo z jiného počítače ve vašem prostředí. Zkopírujte skript do pracovního procesu.

   Skript New-OnPremiseHybridWorker. ps1 vyžaduje během provádění následující parametry:

   * *AutomationAccountName* (povinné): název vašeho účtu Automation.
   * *AAResourceGroupName* (povinné): název skupiny prostředků, která je přidružená k vašemu účtu Automation.
   * *OMSResourceGroupName* (volitelné): název skupiny prostředků pro pracovní prostor Log Analytics. Pokud tato skupina prostředků není zadaná, použije se *AAResourceGroupName* .
   * *HybridGroupName* (povinné): název skupiny Hybrid Runbook Worker, kterou zadáte jako cíl pro Runbooky, které podporují tento scénář.
   * *SubscriptionId* (povinné): ID předplatného Azure, ve kterém je váš účet Automation.
   * *Pracovní prostor* (volitelné): Název Log Analytics pracovního prostoru. Pokud nemáte pracovní prostor Log Analytics, skript ho vytvoří a nakonfiguruje.

   > [!NOTE]
   > Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
   >
   > Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

2. V počítači otevřete **prostředí Windows PowerShell** z obrazovky **Start** v režimu správce.
3. Z prostředí příkazového řádku PowerShellu přejděte do složky, která obsahuje skript, který jste stáhli. Změňte hodnoty parametrů *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*a *-Workspace*. Potom spusťte skript.

     > [!NOTE]
     > Po spuštění skriptu budete vyzváni k ověření pomocí Azure. *Musíte* se přihlásit pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Budete vyzváni k vyjádření souhlasu s instalací NuGet a zobrazí se výzva k ověření pomocí přihlašovacích údajů Azure.

5. Po dokončení skriptu se na stránce **Hybrid Worker skupiny** zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce **Hybrid Worker skupiny** a vybrat dlaždici **hybridní pracovní procesy** . Na stránce **hybridní procesy** se zobrazí všichni členové skupiny v seznamu.

### <a name="manual-deployment"></a>Ruční nasazení

Proveďte první dva kroky jednou pro prostředí automatizace a pak opakujte zbývající kroky pro každý pracovní počítač.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. vytvoření pracovního prostoru Log Analytics

Pokud ještě nemáte pracovní prostor Log Analytics, vytvořte ho pomocí pokynů v tématu [Správa pracovního prostoru](../azure-monitor/platform/manage-access.md). Existující pracovní prostor můžete použít, pokud ho už máte.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Přidejte řešení pro automatizaci do pracovního prostoru Log Analytics.

Řešení Automation Azure Monitor logs přidává funkce pro Azure Automation, včetně podpory pro Hybrid Runbook Worker. Když přidáte řešení do svého pracovního prostoru, automaticky ho dokončí do počítače agenta, který budete instalovat v dalším kroku.

Pokud chcete do svého pracovního prostoru přidat řešení **Automation** Azure monitor logs, spusťte následující PowerShell.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. nainstalujte Microsoft Monitoring Agent

Microsoft Monitoring Agent připojuje počítače k Azure Monitor protokolů. Když nainstalujete agenta na místní počítač a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřeba pro Hybrid Runbook Worker.

Chcete-li nainstalovat agenta na místní počítač, postupujte podle pokynů v tématu [připojení počítačů se systémem Windows k Azure monitor protokolů](../log-analytics/log-analytics-windows-agent.md). Tento postup můžete opakovat, pokud chcete do vašeho prostředí přidat více pracovních procesů.

Po úspěšném připojení agenta k Azure Monitor protokoly se zobrazí na kartě **připojené zdroje** na stránce **Nastavení** Log Analytics. Můžete ověřit, že agent správně stáhl řešení automatizace, když má složku s názvem **AzureAutomationFiles** v adresáři C:\Program Files\Microsoft monitoring Agent\Agent. Pokud chcete potvrdit verzi Hybrid Runbook Worker, můžete přejít do složky C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation\ a poznamenat si podsložku \\*verze* .

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Nainstalujte prostředí Runbooku a připojte se Azure Automation

Když přidáte agenta do Azure Monitor protokolů, řešení automatizace vloží modul **HybridRegistration** PowerShell, který obsahuje rutinu **Add-HybridRunbookWorker** . Pomocí této rutiny nainstalujete do počítače prostředí Runbooku a zaregistrujete ho do Azure Automation.

Otevřete relaci PowerShellu v režimu správce a spusťte následující příkazy, abyste naimportovali modul:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Pak spusťte rutinu **Add-HybridRunbookWorker** pomocí následující syntaxe:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informace požadované pro tuto rutinu můžete získat ze stránky **Správa klíčů** v Azure Portal. Tuto stránku otevřete výběrem možnosti **klíče** na stránce **Nastavení** v účtu Automation.

![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Název_skupiny** je název skupiny Hybrid Runbook Worker. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, přidá se.
* **Koncový bod** je položka **URL** na stránce **Správa klíčů** .
* **Token** je položka **primárního přístupového klíče** na stránce **Správa klíčů** .

Pokud chcete získat podrobné informace o instalaci, použijte přepínač **-verbose** s příkazem **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Instalace modulů PowerShellu

Runbooky můžou používat libovolnou z aktivit a rutin definovaných v modulech, které jsou nainstalované ve vašem Azure Automation prostředí. Tyto moduly nejsou automaticky nasazeny do místních počítačů, takže je musíte nainstalovat ručně. Výjimkou je modul Azure, který je ve výchozím nastavení nainstalovaný a poskytuje přístup k rutinám pro všechny služby a aktivity Azure pro Azure Automation.

Vzhledem k tomu, že primárním účelem funkce Hybrid Runbook Worker je Správa místních prostředků, pravděpodobně budete muset nainstalovat moduly, které tyto prostředky podporují. Informace o instalaci modulů prostředí Windows PowerShell najdete v tématu [Instalace modulů](/powershell/scripting/developer/windows-powershell). 

Moduly, které jsou nainstalovány, musí být v umístění, na které odkazuje proměnná prostředí **PSModulePath** , aby je hybridní pracovní proces mohl automaticky importovat. Další informace najdete v tématu [Úprava instalační cesty PSModulePath](/powershell/scripting/developer/windows-powershell).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Pokyny, jak odebrat procesy Hybrid Runbook Worker, najdete v tématu [odebrání Azure Automationch procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Informace o řešení potíží s procesy Hybrid Runbook Worker najdete v tématu [řešení potíží s Windows Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)
* Další kroky, jak řešit problémy s Update Management, najdete v článku [Update Management: řešení potíží](troubleshoot/update-management.md).
