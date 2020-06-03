---
title: Nasazení Windows Hybrid Runbook Worker v Azure Automation
description: V tomto článku se dozvíte, jak nasadit Hybrid Runbook Worker, které můžete použít ke spouštění Runbooků v počítačích se systémem Windows v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 8a21f6a58e8dc657d3b60aac33661504363072e2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309911"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker Windows

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více určených počítačů. Tento článek popisuje, jak nasadit Hybrid Runbook Worker na počítači s Windows.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalace a konfigurace Windows Hybrid Runbook Worker

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker systému Windows, můžete použít jednu z následujících metod.

* Pro virtuální počítače Azure nainstalujte agenta Log Analytics pro Windows pomocí [rozšíření virtuálního počítače pro Windows](../virtual-machines/extensions/oms-windows.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics pomocí šablony Azure Resource Manager nebo PowerShellu. Po instalaci agenta je možné virtuální počítač přidat do skupiny Hybrid Runbook Worker v účtu Automation. Viz kroky 3 a 4 v části [Ruční nasazení](#manual-deployment) .

* K úplnému automatizaci procesu konfigurace počítače se systémem Windows použijte Runbook služby Automation. Toto je doporučená metoda pro počítače ve vašem datovém centru nebo jiném cloudovém prostředí.

* Postupujte podle podrobných pokynů, jak ručně nainstalovat a nakonfigurovat roli Hybrid Runbook Worker na VIRTUÁLNÍm počítači mimo Azure.

> [!NOTE]
> Chcete-li spravovat konfiguraci serverů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné přidat servery jako uzly DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimální požadavky pro Windows Hybrid Runbook Worker

Minimální požadavky pro Windows Hybrid Runbook Worker jsou:

* Windows Server 2012 nebo novější
* Windows PowerShell 5,1 nebo novější ([Stáhnout WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 nebo novější
* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="network-configuration"></a>Konfigurace sítě

Další požadavky na síť pro Hybrid Runbook Worker najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Povolování serverů pro správu pomocí konfigurace stavu Azure Automation

Informace o povolování serverů pro správu pomocí konfigurace stavu Azure Automation najdete v tématu [Povolení počítačů pro správu pomocí konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

Povolením Azure Automation [Update Management](automation-update-management.md) se automaticky nakonfiguruje jakýkoli počítač s Windows, který je připojený k vašemu Log Analytics pracovnímu prostoru jako Hybrid Runbook Worker pro podporu aktualizací sady Runbook. Tento pracovní proces ale není zaregistrovaný u žádné Hybrid Runbook Worker skupiny, které už jsou ve vašem účtu Automation definované.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Přidání počítače do skupiny Hybrid Runbook Worker

Pracovní počítač můžete přidat do skupiny Hybrid Runbook Worker v účtu Automation. Všimněte si, že je potřeba podporovat Runbooky Automation, pokud používáte stejný účet pro funkci Azure Automation i pro členství v Hybrid Runbook Worker skupině. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

## <a name="automated-deployment"></a>Automatizované nasazení

V cílovém počítači proveďte následující kroky, které automatizují instalaci a konfiguraci role Windows Hybrid Worker.

### <a name="step-1---download-the-powershell-script"></a>Krok 1 – stažení skriptu PowerShellu

Stáhněte si skript **New-OnPremiseHybridWorker. ps1** z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Stahování by mělo být přímo z počítače, na kterém běží role Hybrid Runbook Worker, nebo z jiného počítače ve vašem prostředí. Po stažení skriptu ho zkopírujte do pracovního procesu. Skript **New-OnPremiseHybridWorker. ps1** používá parametry popsané níže během provádění.

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

> [!NOTE]
> Při povolování funkcí Azure Automation podporuje jenom určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Krok 2 – otevření prostředí příkazového řádku Windows PowerShellu

Spusťte **prostředí Windows PowerShell** z obrazovky **Start** v režimu správce.

### <a name="step-3---run-the-powershell-script"></a>Krok 3 – spuštění skriptu PowerShellu

V prostředí příkazového řádku PowerShellu přejděte do složky, která obsahuje skript, který jste stáhli. Změňte hodnoty parametrů `AutomationAccountName` ,,,, `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` a `WorkspaceName` . Potom spusťte skript.

Po spuštění skriptu budete vyzváni k ověření pomocí Azure. Musíte se přihlásit pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Krok 4 – instalace NuGetu

Budete vyzváni k vyjádření souhlasu s instalací NuGet a k ověření pomocí přihlašovacích údajů Azure. Pokud nemáte nejnovější verzi NuGet, můžete ji získat z [dostupných verzí distribuce NuGet](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Krok 5 – ověření nasazení

Po dokončení skriptu se na stránce Hybrid Worker skupiny zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce Hybrid Worker skupiny a kliknout na dlaždici **hybridní pracovní procesy** . Na stránce hybridní pracovní procesy uvidíte všechny členy uvedené skupiny.

## <a name="manual-deployment"></a>Ruční nasazení

Na cílovém počítači proveďte první dva kroky pro vaše prostředí automatizace. Pak proveďte zbývající kroky pro každý pracovní počítač.

### <a name="step-1---create-a-log-analytics-workspace"></a>Krok 1 – Vytvoření pracovního prostoru Log Analytics

Pokud ještě nemáte pracovní prostor Log Analytics, přečtěte si [pokyny k návrhu protokolu Azure monitor](../azure-monitor/platform/design-logs-deployment.md) před vytvořením pracovního prostoru.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Krok 2 – Přidání funkce Azure Automation do pracovního prostoru Log Analytics

Funkce automatizace přidává funkce pro Azure Automation, včetně podpory Hybrid Runbook Worker. Pokud povolíte funkci Azure Automation v pracovním prostoru Log Analytics, automaticky se do počítače agenta přiřadí komponenty pracovního procesu.

Pokud chcete do svého pracovního prostoru přidat funkci Azure Automation, například Update Management, spusťte následující rutinu prostředí PowerShell:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Krok 3 – Instalace agenta Log Analytics pro Windows

Agent Log Analytics pro systém Windows připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do svého počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřebné pro Hybrid Runbook Worker.

Chcete-li nainstalovat agenta v počítači, postupujte podle pokynů v tématu [připojení počítačů se systémem Windows k Azure monitor protokolů](../log-analytics/log-analytics-windows-agent.md). Tento postup můžete opakovat, pokud chcete do vašeho prostředí přidat více pracovních procesů.

Jakmile se agent úspěšně připojí k pracovnímu prostoru Log Analytics po několika minutách, můžete spustit následující dotaz, který ověří, jestli odesílá data prezenčního signálu do pracovního prostoru.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Ve výsledcích hledání byste měli zobrazit záznamy prezenčního signálu pro daný počítač, což znamená, že je připojený a oznamuje službě zprávy. Ve výchozím nastavení každý Agent přepošle záznam prezenčního signálu do přiřazeného pracovního prostoru. Pomocí následujících kroků dokončete instalaci a instalaci agenta.

1. Povolením funkce přidejte počítač agenta. Viz [Povolení počítačů v pracovním prostoru](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Ověřte, že agent správně stáhl funkci Azure Automation. 
3. Verzi Hybrid Runbook Worker ověříte tak, že přejdete do složky **C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation** a poznamenejte si podsložku **verze** .

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Krok 4 – instalace prostředí Runbooku a připojení k Azure Automation

Když nakonfigurujete agenta tak, aby nahlásil do Log Analytics pracovního prostoru, funkce Azure Automation vloží `HybridRegistration` modul PowerShellu, který obsahuje `Add-HybridRunbookWorker` rutinu. Pomocí této rutiny nainstalujete do počítače prostředí Runbooku a zaregistrujete ho do Azure Automation.

Otevřete relaci PowerShellu v režimu správce a spuštěním následujících příkazů Importujte modul.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Nyní spusťte `Add-HybridRunbookWorker` rutinu pomocí následující syntaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informace požadované pro tuto rutinu můžete získat ze stránky Správa klíčů v Azure Portal. Otevřete tuto stránku tak, že vyberete **klíče** na stránce nastavení v účtu Automation.

![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pro `GroupName` parametr použijte název skupiny Hybrid Runbook Worker. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, přidá se.
* Pro `EndPoint` parametr použijte položku **Adresa URL** na stránce Správa klíčů.
* Pro `Token` parametr použijte položku **primárního přístupového klíče** na stránce Správa klíčů.
* V případě potřeby nastavte `Verbose` parametr pro příjem podrobností o instalaci.

### <a name="step-5----install-powershell-modules"></a>Krok 5 – Instalace modulů PowerShellu

Runbooky můžou používat jakékoli aktivity a rutiny definované v modulech nainstalovaných ve vašem Azure Automationovém prostředí. Protože tyto moduly nejsou automaticky nasazeny do místních počítačů, je nutné je nainstalovat ručně. Výjimkou je modul Azure. Tento modul se instaluje ve výchozím nastavení a poskytuje přístup k rutinám pro všechny služby a aktivity Azure pro Azure Automation.

Vzhledem k tomu, že primárním účelem Hybrid Runbook Worker je spravovat místní prostředky, pravděpodobně budete muset nainstalovat moduly, které tyto prostředky podporují, zejména `PowerShellGet` modul. Informace o instalaci modulů prostředí Windows PowerShell najdete v tématu [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Moduly, které jsou nainstalovány, musí být v umístění, na `PSModulePath` které odkazuje proměnná prostředí, aby je hybridní pracovní proces mohl automaticky importovat. Další informace najdete v tématu [Instalace modulů v PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>Odebrání Hybrid Runbook Worker z místního počítače s Windows

1. V Azure Portal přejdete do svého účtu Automation.
2. V části **Nastavení účtu**vyberte **klíče** a poznamenejte si hodnoty **adresy URL** a **primárního přístupového klíče**.

3. Otevřete relaci PowerShellu v režimu správce a spusťte následující příkaz s hodnotami adresy URL a primárního přístupového klíče. Použijte `Verbose` parametr pro podrobný protokol procesu odebrání. K odebrání zastaralých počítačů ze skupiny Hybrid Worker Použijte volitelný `machineName` parametr.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
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
