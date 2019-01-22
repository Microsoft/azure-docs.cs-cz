---
title: Funkce Hybrid Runbook Worker služby Azure Automation ve Windows
description: Tento článek obsahuje informace o instalaci Azure Automation Hybrid Runbook Worker, který používáte ke spuštění sady runbook v počítačích se systémem Windows v místním datovém centru nebo v cloudovém prostředí.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2d9f1b99407f5c94581a3108c785292e9928cbb4
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432316"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Nasazení Windows Hybrid Runbook Worker

Funkce Hybrid Runbook Worker služby Azure Automation můžete použít ke spuštění sady runbook přímo na počítači, který je hostitelem role a s prostředky v prostředí ke správě místních prostředků. Sady Runbook jsou uloženy a spravovaná ve službě Azure Automation a pak doručí jeden nebo více počítačů určené. Tento článek popisuje postup instalace procesu Hybrid Runbook Worker na počítači s Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalace procesu Hybrid Runbook Worker Windows

Instalace a konfigurace Windows Hybrid Runbook Worker, můžete použít dvě metody. Doporučený postup je použít runbook služby Automation zcela automatizovat proces konfigurace počítače Windows. Druhá metoda sleduje podrobný postup, jak ručně nainstalovat a nakonfigurovat role.

> [!NOTE]
> Ke správě konfigurace serverů, které podporují role Hybrid Runbook Worker s Desired State Configuration (DSC), musíte je přidat jako uzly DSC.

Minimální požadavky pro Windows Hybrid Runbook Worker jsou:

* Windows Server 2012 nebo novější.
* Prostředí Windows PowerShell 5.1 nebo novější ([stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* Rozhraní .NET framework 4.6.2 nebo novější.
* Dvě jádra.
* 4 GB paměti RAM.
* Port 443 (odchozí).

Pokud chcete získat další požadavky na síť pro Hybrid Runbook Worker, naleznete v tématu [konfiguraci sítě](automation-hybrid-runbook-worker.md#network-planning).

Další informace o připojení serverů pro správu pomocí DSC najdete v tématu [připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md).
Pokud povolíte [řešení Update Management](../operations-management-suite/oms-solution-update-management.md), libovolného počítače Windows, který je připojený k pracovnímu prostoru Azure Log Analytics se automaticky nakonfiguruje jako Hybrid Runbook Worker pro podporu runbooků, které jsou zahrnuté v tomto řešení. Však není zaregistrovaný u žádných skupin Hybrid Worker, který je již definován ve vašem účtu Automation. 

Počítač je přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation pro podporu runbooků Automation za předpokladu, že používáte stejný účet pro řešení a členství ve skupině Hybrid Runbook Worker. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

Po úspěšně nasazení procesu runbook worker, zkontrolujte [spouštění runbooků v procesu Hybrid Runbook Worker](automation-hrw-run-runbooks.md) na další informace o konfiguraci runbooky pro automatizaci procesů do vašeho místního datového centra nebo jiné cloudové prostředí.

### <a name="automated-deployment"></a>Automatické nasazení

Proveďte následující kroky k automatizaci instalace a konfigurace role Windows Hybrid Worker:

1. Stáhněte si skript New-OnPremiseHybridWorker.ps1 z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) přímo z počítače se spuštěnou rolí pracovního procesu Hybrid Runbook Worker nebo z jiného počítače ve vašem prostředí. Zkopírujte skript do pracovního procesu.

   Skript New-OnPremiseHybridWorker.ps1 vyžaduje následující parametry během spuštění:

   * *AutomationAccountName* (mandatory): Název účtu služby Automation.
   * *AAResourceGroupName* (mandatory): Název skupiny prostředků, který je spojen s vaším účtem Automation.
   * *OMSResourceGroupName* (optional): Název skupiny prostředků pro pracovní prostor Log Analytics. Pokud tato skupina prostředků není zadán, *AAResourceGroupName* se používá.
   * *HybridGroupName* (mandatory): Název skupiny Hybrid Runbook Worker, který zadáte jako cíl pro sady runbook, která podporuje tento scénář.
   * *SubscriptionID* (povinné): ID předplatného Azure, které váš účet služby Automation.
   * *WorkspaceName* (volitelné): Název pracovního prostoru Log Analytics. Pokud nemáte pracovní prostor Log Analytics, skript vytvoří a nakonfiguruje jednu.

     > [!NOTE]
     > V současné době pouze oblasti služby Automation, které podporuje integraci s Log Analytics jsou **Austrálie – jihovýchod**, **USA – východ 2**, **jihovýchodní Asie**, a  **Západní Evropa**. Pokud váš účet Automation není v těchto oblastech, skript vytvoří pracovní prostor Log Analytics ale vás upozorní, že jej nelze propojit dohromady.

2. V počítači, otevřete **prostředí Windows PowerShell** z **Start** obrazovku v režimu správce.
3. Z prostředí příkazového řádku Powershellu přejděte do složky, která obsahuje skript, který jste stáhli. Změňte hodnoty pro parametry *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, a *- WorkspaceName*. Potom spusťte skript.

     > [!NOTE]
     > Budete vyzváni k ověření pomocí Azure po spuštění skriptu. Můžete *musí* Přihlaste se pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Budete vyzváni k instalaci balíčku NuGet svůj souhlas, a budete vyzváni k ověření pomocí přihlašovacích údajů Azure.

5. Po dokončení skriptu **skupiny hybridních pracovních procesů** stránka zobrazuje nové skupiny a počet členů. Pokud je existující skupiny, se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupiny hybridních pracovních procesů** stránku a vybrat **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** stránku, uvidíte každého člena skupiny uvedeny.

### <a name="manual-deployment"></a>Ruční nasazení

První dva kroky pro automatizovaného prostředí provést jednou a potom zopakujte zbývající kroky pro každý počítač pracovního procesu.

#### <a name="1-create-a-log-analytics-workspace"></a>1. Vytvoření pracovního prostoru Log Analytics

Pokud ještě nemáte pracovní prostor Log Analytics, vytvořte ho podle pokynů v [Správa pracovního prostoru](../azure-monitor/platform/manage-access.md). Pokud ještě nemáte, můžete použít existující pracovní prostor.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Přidejte řešení služby Automation k pracovnímu prostoru Log Analytics

Řešení do služby Log Analytics přidávají funkčnost. Řešení služby Automation přidává funkce pro Azure Automation, včetně podpory pro Hybrid Runbook Worker. Když přidáte řešení do pracovního prostoru, automaticky se doručí součásti pracovních procesů na počítači agenta, který nainstalujete v dalším kroku.

Chcete-li přidat **automatizace** řešení do pracovního prostoru Log Analytics, postupujte podle pokynů na adrese [přidáte řešení pomocí Galerie řešení](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Nainstalovat agenta Microsoft Monitoring Agent.

Microsoft Monitoring Agent připojí počítače k Log Analytics. Při instalaci agenta ve vašem místním počítači a připojte ho do pracovního prostoru, automaticky stáhne součásti, které jsou požadovány pro Hybrid Runbook Worker.

Nainstalujte agenta na místním počítači, postupujte podle pokynů v [počítače Windows se připojit ke službě Log Analytics](../log-analytics/log-analytics-windows-agent.md). Tento proces pro více počítačů, chcete-li přidat více pracovních procesů pro vaše prostředí, můžete opakovat.

Agent se úspěšně připojilo ke službě Log Analytics, je uvedený na **připojené zdroje** kartu služby Log Analytics **nastavení** stránky. Můžete ověřit, že agent správně stáhla řešení služby Automation je složka s názvem **AzureAutomationFiles** v C:\Program Files\Microsoft Monitoring Agent\Agent. K potvrzení verze funkce Hybrid Runbook Worker, můžete přejít na C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ a Všimněte si, \\ *verze* podsložky.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalaci prostředí runbooku a připojte se k Azure Automation.

Když přidáte agenta ke službě Log Analytics, řešení služby Automation nabízených oznámení **HybridRegistration** modul prostředí PowerShell, který obsahuje **Add-HybridRunbookWorker** rutiny. Tuto rutinu použijte k instalaci prostředí runbooku na počítač a zaregistrovat ji pomocí služby Azure Automation.

Otevřete relaci Powershellu v režimu správce a spusťte následující příkazy pro import modulu:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Spusťte **Add-HybridRunbookWorker** rutiny s následující syntaxí:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Umožňuje získat všechny informace požadované pro tuto rutinu **Správa klíčů** stránky na webu Azure Portal. Výběrem otevřete tuto stránku **klíče** možnost **nastavení** stránku ve svém účtu Automation.

![Stránka "Správa klíčů"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** je název skupiny Hybrid Runbook Worker. Pokud tato skupina již existuje v účtu Automation, počítač se přidá do ní. Pokud tato skupina neexistuje, je přidána.
* **Koncový bod** je **URL** položky na **Správa klíčů** stránky.
* **Token** je **primární přístupový klíč** položky na **Správa klíčů** stránky.

Chcete-li zobrazit podrobné informace o instalaci, použijte **-Verbose** přepínači s **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Nainstalujte moduly Powershellu

Sady Runbook můžete použít některou z aktivit a rutiny, které jsou definovány v modulech, které jsou nainstalovány ve vašem prostředí Azure Automation. Tyto moduly nejsou automaticky nasazují do místních počítačů, takže je nutné nainstalovat ručně. Výjimkou je modul Azure, která je ve výchozím nastavení nainstalované a poskytuje přístup k rutiny pro všechny služby Azure a aktivity pro Azure Automation.

Protože primárním účelem funkce Hybrid Runbook Worker je můžou spravovat místní prostředky, budete pravděpodobně muset nainstalovat moduly, které podporují tyto prostředky. Informace o instalaci moduly Windows Powershellu najdete v tématu [instalaci modulů](https://msdn.microsoft.com/library/dd878350.aspx). 

Moduly, které jsou nainstalované musí být v umístění odkazuje **PSModulePath** proměnnou prostředí tak, aby je automaticky importovat procesu hybrid worker. Další informace najdete v tématu [úpravy Instalační_cesta PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Řešení potíží

Další řešení potíží s procesy Hybrid Runbook Worker, najdete v článku [řešení potíží s Windows Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#windows)

Další pokyny o tom, jak řešit problémy pomocí správy aktualizací najdete v tématu [Update Management: řešení potíží s](troubleshoot/update-management.md).

## <a name="next-steps"></a>Další postup

* Další informace o konfiguraci runbooky pro automatizaci procesů do vašeho místního datového centra nebo jiné cloudové prostředí, najdete v článku [spouštění runbooků v procesu Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Pokyny k odebrání funkce Hybrid Runbook Worker, naleznete v tématu [odebrat Azure Automation Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).

