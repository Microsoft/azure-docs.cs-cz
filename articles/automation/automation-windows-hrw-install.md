---
title: Funkce Hybrid Runbook Worker služby Azure Automation ve Windows
description: Tento článek obsahuje informace o instalaci hybridního pracovníka runbooku Azure Automation, který můžete použít ke spuštění runbooků v počítačích se systémem Windows v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2bd9b4f46e28a28f99045319d8ac606cdcee7216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536781"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení pracovníka hybridní hotonové ho schodišti Windows

Pomocí funkce Hybridní runbook worker azure automation můžete spustit runbooky přímo v počítači, který je hostitelem role a proti prostředkům v prostředí ke správě těchto místních prostředků. Azure Automation ukládá a spravuje runbooky a pak je doručuje do jednoho nebo více určených počítačů. Tento článek popisuje, jak nasadit hybridní pracovník runbooku v počítači se systémem Windows.

Po úspěšném nasazení pracovníka sady Runbook zkontrolujte [runbooky na hybridním pracovníku sady Runbook,](automation-hrw-run-runbooks.md) abyste zjistili, jak nakonfigurovat sady Runbook pro automatizaci procesů v místním datovém centru nebo jiném cloudovém prostředí.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalace a konfigurace pracovníka hybridního schytu systému Windows

Chcete-li nainstalovat a nakonfigurovat pracovníka hybridnísady Runbook systému Windows, můžete použít jednu z následujících metod.

* Pro virtuální počítače Azure nainstalujte agenta Log Analytics pro Windows pomocí [rozšíření virtuálního počítače pro Windows](../virtual-machines/extensions/oms-windows.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics pomocí šablony Azure Resource Manager nebo PowerShellu. Po instalaci agenta lze virtuální počítač přidat do skupiny Hybridní pracovní prostředí runbooku ve vašem účtu Automation. Postup 3 a 4 naleznete v části [Ruční nasazení.](#manual-deployment)

* Pomocí runbooku Automatizace můžete zcela automatizovat proces konfigurace počítače se systémem Windows. Toto je doporučená metoda pro počítače v datovém centru nebo v jiném cloudovém prostředí.

* Pomocí podrobného postupu ručně nainstalujte a nakonfigurujte roli hybridního pracovníka sady Runbook na vašem virtuálním počítači mimo Azure.

> [!NOTE]
> Chcete-li spravovat konfiguraci serverů, které podporují roli hybridního pracovníka runbooku s konfigurací požadovaného stavu (DSC), je nutné je přidat jako uzly DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Minimální požadavky na pracovníka hybridní sady Runbook systému Windows

Minimální požadavky pro pracovníka hybridního sady Runbook systému Windows jsou:

* Windows Server 2012 nebo novější
* Windows PowerShell 5.1 nebo novější[(stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 nebo novější
* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="network-configuration"></a>Konfigurace sítě

Další požadavky na síť pro pracovníka hybridní sady Runbook naleznete [v tématu Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Připojení serveru pro správu pomocí automation dsc

Informace o onboardingových serverech pro správu pomocí DSC najdete [v tématu Onboarding machines for management by Azure Automation DSC](automation-dsc-onboarding.md).

Povolením [řešení pro správu aktualizací](../operations-management-suite/oms-solution-update-management.md) se automaticky nakonfiguruje jakýkoli počítač se systémem Windows, který je připojen k pracovnímu prostoru Log Analytics jako hybridní pracovník sady Runbook pro podporu runbooků zahrnutých v řešení. Tento pracovník však není registrován u žádné skupiny pracovníků hybridního runbooku, které jsou již definovány ve vašem účtu automation.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Přidání počítače do skupiny Hybridní pracovní proces runbooku

Pracovní počítač můžete přidat do skupiny Hybridní pracovní proces runbooku ve vašem účtu Automation. Všimněte si, že je nutné podporovat automatizace runbooků tak dlouho, dokud používáte stejný účet pro řešení a hybridní Runbook Worker členství ve skupině. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

## <a name="automated-deployment"></a>Automatizované nasazení

V cílovém počítači proveďte následující kroky k automatizaci instalace a konfigurace role hybridního pracovního procesu systému Windows.

### <a name="step-1---download-the-powershell-script"></a>Krok 1 – Stažení skriptu PowerShellu

Stáhněte si skript **New-OnPremiseHybridWorker.ps1** z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Stahování by mělo probíhat přímo z počítače s rolí Hybridní pracovník runbooku nebo z jiného počítače ve vašem prostředí. Po stažení skriptu jej zkopírujte do pracovníka. Skript **New-OnPremiseHybridWorker.ps1** používá parametry popsané níže během provádění.

| Parametr | Status | Popis |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Povinné | Název skupiny prostředků, která je přidružena k vašemu účtu Automation. |
| `AutomationAccountName` | Povinné | Název účtu Automation.
| `Credential` | Nepovinné | Přihlašovací údaje, které se mají použít při přihlášení do prostředí Azure. |
| `HybridGroupName` | Povinné | Název skupiny hybridního pracovníka sady Runbook, který zadáte jako cíl pro sady Runbook, které podporují tento scénář. |
| `OMSResourceGroupName` | Nepovinné | Název skupiny prostředků pro pracovní prostor Log Analytics. Pokud tato skupina prostředků není zadána, `AAResourceGroupName` použije se hodnota. |
| `SubscriptionID` | Povinné | Identifikátor předplatného Azure přidruženého k vašemu účtu Automation. |
| `TenantID` | Nepovinné | Identifikátor organizace tenanta přidruženéka k vašemu účtu Automation. |
| `WorkspaceName` | Nepovinné | Název pracovního prostoru Analýzy protokolů. Pokud nemáte pracovní prostor Log Analytics, skript vytvoří a nakonfiguruje jeden. |

> [!NOTE]
> Při povolení řešení Azure Automation podporuje pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování naleznete v tématu [Mapování oblasti pro účet Automatizace a pracovní prostor Log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Krok 2 – Otevření prostředí příkazového řádku prostředí Windows PowerShell

Spusťte **prostředí Windows PowerShell** z **úvodní** obrazovky v režimu správce.

### <a name="step-3---run-the-powershell-script"></a>Krok 3 – Spuštění skriptu Prostředí PowerShell

V prostředí příkazového řádku prostředí PowerShell přejděte do složky obsahující skript, který jste stáhli. Změňte hodnoty parametrů `AutomationAccountName`, `AAResourceGroupName` `OMSResourceGroupName`, `HybridGroupName` `SubscriptionID`, `WorkspaceName`a . Pak spusťte skript.

Po spuštění skriptu se zobrazí výzva k ověření pomocí Azure. Musíte se přihlásit pomocí účtu, který je členem role Správci předplatného a spolusprávcem předplatného.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Krok 4 – Instalace nugetu

Budete vyzváni k souhlasu s instalací NuGet a k ověření pomocí přihlašovacích údajů Azure. Pokud nemáte nejnovější verzi NuGet, můžete ji získat z [dostupných nugetových distribučních verzí](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Krok 5 – Ověření nasazení

Po dokončení skriptu se na stránce Hybridní pracovní skupiny zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, počet členů se zintácí. Skupinu můžete vybrat ze seznamu na stránce Hybridní pracovní skupiny a vybrat dlaždici **Hybridní pracovníci.** Na stránce Hybridní pracovníci uvidíte každého člena skupiny uvedeného.

## <a name="manual-deployment"></a>Ruční nasazení

Na cílovém počítači proveďte první dva kroky jednou pro prostředí automatizace. Potom proveďte zbývající kroky pro každý pracovní počítač.

### <a name="step-1---create-a-log-analytics-workspace"></a>Krok 1 – Vytvoření pracovního prostoru analýzy protokolů

Pokud ještě nemáte pracovní prostor Log Analytics, zkontrolujte [pokyny k návrhu protokolu sledování Azure](../azure-monitor/platform/design-logs-deployment.md) před vytvořením pracovního prostoru.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Krok 2 – přidání řešení automatizace do pracovního prostoru Log Analytics

Řešení Automatizace přidává funkce pro Azure Automation, včetně podpory pro hybridní pracovník runbooku. Když přidáte řešení do pracovního prostoru Log Analytics, automaticky odešle do počítače agenta pracovní součásti, které nainstalujete, jak je popsáno v dalším kroku.

Chcete-li přidat řešení automatizace do pracovního prostoru, spusťte následující rutinu prostředí PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Krok 3 – Instalace agenta analýzy protokolů pro Windows

Agent Log Analytics pro Windows připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou požadovány pro pracovníka hybridního runbooku.

Chcete-li nainstalovat agenta do počítače, postupujte podle pokynů v části [Připojení počítačů se systémem Windows k protokolům programu Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Tento proces můžete opakovat pro více počítačů přidat více pracovníků do vašeho prostředí.

Pokud se agent po několika minutách úspěšně připojil k pracovnímu prostoru Analýzy protokolů, můžete spustit následující dotaz a ověřit, zda odesílá data prezenčního signálu do pracovního prostoru.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro počítač, což znamená, že je připojen a hlásí se ke službě. Ve výchozím nastavení každý agent předá záznam prezenčního signálu svému přiřazenému pracovnímu prostoru. 

K dokončení instalace a instalace agenta použijte následující kroky.

1. Povolte řešení pro palubní počítač agenta. Viz [Palubní stroje v pracovním prostoru](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Ověřte, zda agent správně stáhl řešení automatizace. Měl by mít složku s názvem **AzureAutomationFiles** v **C:\Program Files\Microsoft Monitoring Agent\Agent**. 
3. Chcete-li potvrdit verzi hybridního pracovníka sady Runbook, přejděte na **c:\programové soubory\agenta monitorování společnosti Microsoft\agenta\AzureAutomation** a poznamenejte si podsložku **verze.**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Krok 4 – Instalace prostředí sady Runbook a připojení k Azure Automation

Když nakonfigurujete agenta pro hlášení do pracovního `HybridRegistration` prostoru Analýzy protokolů, řešení automatizace posune dolů modul Prostředí PowerShell, který obsahuje rutinu. `Add-HybridRunbookWorker` Pomocí této rutiny nainstalujte prostředí sady Runbook do počítače a zaregistrujte ho pomocí Azure Automation.

Otevřete relaci prostředí PowerShell v režimu správce a importujte modul pomocí následujících příkazů.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Nyní spusťte rutinu `Add-HybridRunbookWorker` pomocí následující syntaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Informace požadované pro tuto rutinu můžete získat na stránce Spravovat klíče na webu Azure Portal. Otevřete tuto stránku výběrem **kláves** na stránce Nastavení v účtu Automation.

![Stránka Spravovat klíče](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pro `GroupName` parametr použijte název skupiny Hybrid Runbook Worker. Pokud tato skupina již v účtu Automatizace existuje, bude do ní přidán aktuální počítač. Pokud tato skupina neexistuje, je přidána.
* Pro `EndPoint` parametr použijte položku **URL** na stránce Spravovat klíče.
* Pro `Token` parametr použijte položku **PRIMÁRNÍ PŘÍSTUPOVÝ KLÍČ** na stránce Spravovat klíče.
* V případě potřeby `Verbose` nastavte parametr tak, aby přijímali podrobnosti o instalaci.

### <a name="step-5----install-powershell-modules"></a>Krok 5 – Instalace modulů PowerShellu

Sady Runbook můžou používat některou z aktivit a rutin definovaných v modulech nainstalovaných v prostředí Azure Automation. Vzhledem k tomu, že tyto moduly nejsou automaticky nasazeny do místních počítačů, je nutné je nainstalovat ručně. Výjimkou je modul Azure. Tento modul se instaluje ve výchozím nastavení a poskytuje přístup k rutinám pro všechny služby Azure a aktivity pro Azure Automation.

Vzhledem k tomu, že primárním účelem funkce Hybridní pracovník sady Runbook je správa místních `PowerShellGet` prostředků, budete s největší pravděpodobností muset nainstalovat moduly, které podporují tyto prostředky, zejména modul. Informace o instalaci modulů prostředí Windows PowerShell naleznete v [tématu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Moduly, které jsou nainstalovány, musí `PSModulePath` být v umístění, na které odkazuje proměnná prostředí, aby je hybridní pracovník mohl automaticky importovat. Další informace naleznete [v tématu Instalace modulů v aplikaci PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci sad Runbook pro automatizaci procesů v místním datovém centru nebo jiném cloudovém prostředí najdete v [tématu Spuštění runbooků na hybridním pracovníkovi sady Runbook](automation-hrw-run-runbooks.md).
* Pokyny k odebrání hybridních pracovníků runbooku najdete v tématu [Odebrání hybridních pracovníků sady Runbook Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Informace o řešení potíží s hybridními pracovníky runbooku naleznete [v tématu Poradce při potížích s pracovníky hybridního spouštění systému Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Další kroky při řešení potíží se správou aktualizací naleznete v [tématu Správa aktualizací: poradce při potížích](troubleshoot/update-management.md).
