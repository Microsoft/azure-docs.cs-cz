---
title: Funkce Hybrid Runbook Worker služby Azure Automation ve Windows
description: Tento článek obsahuje informace o instalaci služby Azure Automation hybridní pracovní proces Runbooku, můžete použít ke spuštění sady runbook na počítačích se systémem Windows ve vaší místní datacenter nebo cloudového prostředí.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e449e6e457c4fa568b5a4de5823014b4dcea82d0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063797"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Windows Hybrid Runbook Worker

Ke spuštění sady runbook přímo na počítači, který je hostitelem role a s prostředky v prostředí pro správu těchto místních prostředků, můžete použít funkci hybridní pracovní proces Runbooku automatizace Azure. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači. Tento článek popisuje postup instalace hybridní pracovní proces Runbooku na počítači s Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalace služby Windows Hybrid Runbook Worker

Instalace a konfigurace Windows Hybrid Runbook Worker, můžete použít dvě metody. Doporučený postup je použít runbook služby automatizace zcela automatizovat proces konfigurace počítači se systémem Windows. Druhý způsob je následující podrobný postup při ručně nainstalujte a nakonfigurujte roli.

> [!NOTE]
> Pokud chcete spravovat konfiguraci serverů, které podporují roli hybridní pracovní proces Runbooku s potřeby konfigurace stavu (DSC), musíte je přidat jako uzly DSC.

Jsou minimální požadavky pro Windows Hybrid Runbook Worker:

* Windows Server 2012 nebo novější.
* Prostředí Windows PowerShell 4.0 nebo novější ([stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Doporučujeme, abyste Windows PowerShell 5.1 ([stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) pro větší spolehlivost.
* Rozhraní .NET framework 4.6.2 nebo novější.
* Dvě jádra.
* 4 GB paměti RAM.
* Port 443 (odchozí).

Chcete-li získat další síťové požadavky pro hybridní pracovní proces Runbooku, najdete v části [konfigurace vaší sítě](automation-hybrid-runbook-worker.md#network-planning).

Další informace o připojování servery pro správu s DSC, najdete v části [registrace počítačů pro správu Azure Automation DSC](automation-dsc-onboarding.md).
Pokud povolíte [řešení pro správu aktualizací](../operations-management-suite/oms-solution-update-management.md), jakýkoli počítač systému Windows, který je připojen k pracovní prostor analýzy protokolů Azure je automaticky nakonfigurovaný jako hybridní pracovní proces Runbooku na podporu sady runbook, které jsou zahrnuté v tomto řešení. Však není registrován u žádné skupiny hybridní pracovní proces již definována v účtu Automation. 

Počítač lze přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation na podporu automatizace sady runbook, dokud používáte stejný účet pro řešení a členství ve skupině hybridní pracovní proces Runbooku. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

Po úspěšné nasazení služby runbook worker, zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.

### <a name="automated-deployment"></a>Automatické nasazení

Proveďte následující kroky k automatizaci instalace a konfigurace role hybridního pracovního procesu systému Windows:

1. Stáhněte si nový OnPremiseHybridWorker.ps1 skript z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) přímo v počítači s rolí hybridní pracovní proces Runbooku nebo z jiného počítače ve vašem prostředí. Zkopírujte skript do pracovního procesu.

   Skript New-OnPremiseHybridWorker.ps1 vyžaduje během provádění následující parametry:

   * *AutomationAccountName* (povinné): název účtu Automation.
   * *AAResourceGroupName* (povinné): název skupiny prostředků, který je spojen s vaším účtem Automation.
   * *OMSResourceGroupName* (volitelné): název skupiny prostředků pro pracovní prostor služby Operations Management Suite. Pokud tato skupina prostředků není zadán, *AAResourceGroupName* se používá.
   * *HybridGroupName* (povinné): název skupiny hybridní pracovní proces Runbooku, který zadáte jako cíl pro sady runbook, které podporují tento scénář.
   * *ID předplatného* (povinné): ID předplatného Azure, který váš účet automatizace je v.
   * *WorkspaceName* (volitelné): název pracovního prostoru analýzy protokolů. Pokud nemáte pracovní prostor analýzy protokolů, skript se vytvoří a nakonfiguruje jeden.

     > [!NOTE]
     > V současné době jsou pouze automatizace oblasti, které podporuje integraci s analýzy protokolů **Austrálie – jihovýchod**, **východní USA 2**, **jihovýchodní Asie**, a  **Západní Evropa**. Pokud není váš účet Automation v jednom z těchto oblastí, skript vytvoří pracovní prostor analýzy protokolů ale vás varuje, že ho nelze propojit je společně.

1. V počítači, otevřete **prostředí Windows PowerShell** z **spustit** obrazovky v režimu správce.
1. Z příkazového řádku prostředí PowerShell přejděte do složky, která obsahuje skript, který jste stáhli. Změnit hodnoty pro parametry *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, a *- WorkspaceName*. Spusťte skript.

     > [!NOTE]
     > Se zobrazí výzva k ověření pomocí Azure po spuštění skriptu. Můžete *musí* Přihlaste se pomocí účtu, který je členem role Správci předplatného a spolusprávce předplatného.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Se zobrazí výzva s nainstalovat NuGet a se zobrazí výzva k ověření pomocí přihlašovacích údajů Azure.

1. Po dokončení skriptu **skupinám Hybrid Worker** stránce se zobrazuje nové skupiny a počet členů. Pokud je existující skupiny, se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupinám Hybrid Worker** a vyberte **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** stránky, uvidíte každého člena skupiny uvedené.

### <a name="manual-deployment"></a>Ruční nasazení

Po provedení první dva kroky pro vaše prostředí automatizace a potom zopakujte zbývající kroky pro každý počítač pracovního procesu.

#### <a name="1-create-a-log-analytics-workspace"></a>1. Vytvořit pracovní prostor analýzy protokolů

Pokud ještě nemáte pracovní prostor analýzy protokolů, vytvořte ji pomocí pokynů v [pracovního prostoru Správa](../log-analytics/log-analytics-manage-access.md). Pokud již účet máte, můžete použít existujícímu pracovnímu prostoru.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Přidat řešení služby Automation do pracovního prostoru analýzy protokolů

Řešení do služby Log Analytics přidávají funkčnost. Řešení služby Automation přidá funkce pro Azure Automation, včetně podpory pro hybridní pracovní proces Runbooku. Když přidáte řešení do pracovního prostoru, automaticky se doručí součásti pracovního procesu na počítači agenta, který budete instalovat v dalším kroku.

Chcete-li přidat **automatizace** řešení do pracovního prostoru analýzy protokolů, postupujte podle pokynů v [přidat řešení pomocí Galerie řešení](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalace agenta Microsoft Monitoring Agent

Microsoft Monitoring Agent připojí počítače k analýze protokolů. Při instalaci agenta na místním počítači a připojte ho do pracovního prostoru, it automaticky stáhne součásti, které jsou požadovány pro hybridní pracovní proces Runbooku.

Chcete-li nainstalovat agenta na místním počítači, postupujte podle pokynů v [počítače se systémem Windows se připojit k analýze protokolů](../log-analytics/log-analytics-windows-agent.md). Tento proces pro více počítačů, které chcete přidat více pracovníků pro vaše prostředí, můžete opakovat.

Pokud agenta se úspěšně připojila k analýze protokolů, je uvedena na **připojené zdroje** kartě analýzy protokolů **nastavení** stránky. Můžete ověřit, že agent správně stáhla řešení služby Automation pokud obsahuje složku s názvem **AzureAutomationFiles** v C:\Program Files\Microsoft Monitoring Agent\Agent. Potvrďte verzi hybridní pracovní proces Runbooku, můžete vyhledat C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ a Poznámka \\ *verze* podsložky.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Nainstalovat prostředí runbooku a připojte se k Azure Automation.

Když přidáte agenta k analýze protokolů, vynutí se řešení služby Automation **HybridRegistration** modulu PowerShell, který obsahuje **Add-HybridRunbookWorker** rutiny. Tuto rutinu použijete na počítač nainstalovat prostředí runbooku a zaregistrovat ho u automatizace Azure.

Otevřete relaci prostředí PowerShell v režimu správce a spusťte následující příkazy a naimportujte modul:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Spusťte **Add-HybridRunbookWorker** rutiny s následující syntaxí:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Můžete získat požadované informace o této rutině z **Správa klíčů** na portálu Azure. Otevřete tuto stránku výběrem **klíče** možnost z **nastavení** stránky ve vašem účtu Automation.

!["Správa klíčů" stránky](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** je název skupiny hybridní pracovní proces Runbooku. Pokud tato skupina již existuje v účtu Automation, je přidána s aktuálním počítačem. Pokud tato skupina neexistuje, je přidán.
* **Koncový bod** je **URL** položku na **Správa klíčů** stránky.
* **Token** je **primární přístupový klíč** položku na **Správa klíčů** stránky.

Chcete-li získat podrobné informace o instalaci, použijte **-Verbose** přepínač s **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Instalace modulů prostředí PowerShell

Sady Runbook můžete použít některou z aktivity a rutin, které jsou definované v moduly, které jsou nainstalované ve vašem prostředí Azure Automation. Tyto moduly nejsou automaticky nasadí do místního počítače, je nutné nainstalovat ručně. Výjimkou je modul Azure, která je nainstalována ve výchozím nastavení a poskytuje přístup k rutinám pro všechny služby Azure a aktivity pro Azure Automation.

Protože primárním účelem funkci hybridní pracovní proces Runbooku je ke správě místních prostředků, budete pravděpodobně muset nainstalovat moduly, které podporují tyto prostředky. Informace o instalaci moduly prostředí Windows PowerShell najdete v tématu [instalaci modulů](http://msdn.microsoft.com/library/dd878350.aspx). 

Moduly, které jsou nainstalovány musí být v umístění odkazuje **PSModulePath** proměnnou prostředí tak, aby je automaticky importovat hybridní pracovní proces. Další informace najdete v tématu [úprava cesta instalace PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Řešení potíží

Další informace o řešení potíží s procesy Hybrid Runbook Worker naleznete v tématu [řešení potíží s Windows hybridních pracovních procesech Runbooku](troubleshoot/hybrid-runbook-worker.md#windows)

Další pokyny o tom, jak vyřešit problémy s správy aktualizací najdete v tématu [správy aktualizací: řešení potíží s](troubleshoot/update-management.md).

## <a name="next-steps"></a>Další postup

* Naučte se konfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí, najdete v tématu [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md).
* Pokyny o tom, jak odebrat procesy Hybrid Runbook Worker najdete v tématu [odebrat Azure automatizace procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).