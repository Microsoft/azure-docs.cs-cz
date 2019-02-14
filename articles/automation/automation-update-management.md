---
title: Řešení Update Management v Azure
description: Tento článek je určený k vám pomohou pochopit, jak používat řešení Azure Update Management ke správě aktualizací pro počítače s Windows a Linuxem.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0afb8a09fa9780755bcfeef678b76e176f11b348
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246030"
---
# <a name="update-management-solution-in-azure"></a>Řešení Update Management v Azure

Řešení Update Management ve službě Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače s Windows a Linuxem, které jsou nasazené v Azure, místním prostředí nebo jiných poskytovatelů cloudových služeb. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

Můžete povolit správu aktualizací pro virtuální počítače přímo ze svého účtu Azure Automation. Informace o povolení správy aktualizací pro virtuální počítače ve svém účtu Automation najdete v tématu [Správa aktualizací pro několik virtuálních počítačů](manage-update-multi.md). Můžete také povolit správu aktualizací pro virtuální počítač z virtuálního počítače stránky na webu Azure Portal. Tento scénář je k dispozici pro [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuálních počítačů.

## <a name="solution-overview"></a>Přehled řešení

Počítače, které se spravují přes Update Management použít k provedení vyhodnocení a nasazení aktualizací následující konfigurace:

* Microsoft Monitoring Agent (MMA) pro Windows nebo Linux
* Konfiguraci požadovaného stavu (DSC) PowerShellu pro Linux
* Funkci Hybrid Runbook Worker služby Automation
* Microsoft Update nebo Windows Server Update Services (WSUS) pro počítače s Windows

Následující diagram znázorňuje konceptuální zobrazení chování a toku dat s jak toto řešení vyhodnocuje a aplikuje aktualizace zabezpečení na všechny připojené systémy Windows Server a počítačů s Linuxem v pracovním prostoru:

![Proces správy aktualizací](media/automation-update-management/update-mgmt-updateworkflow.png)

Správa aktualizací umožňuje nativně připojit počítače v několika předplatných ve stejném tenantovi.

Až CVE vydání, trvá 2 – 3 hodiny pro opravu se zobrazí pro počítače s Linuxem pro posouzení.  Pro počítače s Windows trvá 12 až 15 hodin pro opravu zobrazení pro posouzení po byla uvolněna.

Po dokončení kontroly dodržování předpisů pro aktualizace do počítače agenta předává informace hromadné ke službě Azure Log Analytics. Na počítači s Windows se kontrola dodržování předpisů ve výchozím nastavení spouští každých 12 hodin.

Mimo plán kontrol je zahájeno kontroly dodržování předpisů pro aktualizace do 15 minut po restartování, před instalací aktualizací a po instalaci aktualizací agenta MMA.

Pro počítač s Linuxem se kontrola dodržování předpisů ve výchozím nastavení provádí každé tři hodiny. Pokud restartování agenta MMA, kontroly dodržování předpisů je zahájeno do 15 minut.

Řešení podá zprávu o aktuálnosti počítače podle jakým zdrojem je nakonfigurované na synchronizaci se službou. Pokud počítač Windows je nakonfigurovaný tak, že sestavy do služby WSUS, podle toho, když WSUS poslední synchronizaci se službou Microsoft Update, výsledky mohou lišit od co ukazuje Microsoft Updates. Toto chování je stejná pro počítače s Linuxem, které jsou nakonfigurované k ukládání dat do místního úložiště namísto do veřejného úložiště.

> [!NOTE]
> Správně sestavy ve službě správy aktualizací vyžaduje určitých adres URL a portů, aby byla povolená. Další informace o těchto požadavcích najdete v tématu [sítě plánování pro hybridní pracovní procesy](automation-hybrid-runbook-worker.md#network-planning).

Na počítače, které vyžadují aktualizace softwaru, můžete tyto aktualizace nasadit a nainstalovat tak, že vytvoříte plánované nasazení. Aktualizace klasifikované jako *volitelné* nejsou zahrnuté do oboru nasazení pro počítače s Windows. Pouze požadované aktualizace jsou součástí rozsahu nasazení. 

Plánované nasazení definuje, které cílové počítače obdrží použitelné aktualizace, buď tak, že počítače explicitně zadáte, nebo tak, že vyberete [skupinu počítačů](../azure-monitor/platform/computer-groups.md) , který je založen na prohledávání protokolů konkrétní sady počítačů. Zadáte také plán pro schválení a nastavení určitou dobu, během které se můžou aktualizace instalovat.

Aktualizace se instalují podle runbooků ve službě Azure Automation. Nelze zobrazit tyto sady runbook a runbook nevyžadují žádnou konfiguraci. Při vytvoření nasazení aktualizace nasazení aktualizace vytvoří plán, který se spustí hlavní runbook aktualizace v zadanou dobu pro zahrnuté počítače. Hlavní runbook spouští podřízený runbook na každém agentovi k instalaci požadovaných aktualizací.

Datum a čas zadaný v nasazení aktualizací cílové počítače paralelně spustit nasazení. Před instalací Chcete-li ověřit, že se aktualizace stále vyžadují spuštění kontroly. U klientských počítačů služby WSUS Pokud nejsou aktualizace schválené ve službě WSUS, nasazení aktualizace se nezdaří.

Máte na počítači registrován pro správu aktualizací ve více než jeden pracovní prostory Log Analytics (vícenásobné navádění) se nepodporuje.

## <a name="clients"></a>Klienti

### <a name="supported-client-types"></a>Podporované klientské typy

Následující tabulka uvádí seznam podporovaných operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat posouzení.         |
|Windows Server 2008 R2 SP1 a novější (včetně Windows serveru 2012 a 2016)    |Rozhraní .NET framework 4.5.1 nebo novější je povinný. ([Stáhnout rozhraní .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Vyžaduje se Windows PowerShell 4.0 nebo novější. ([Stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 se doporučuje pro zvýšení spolehlivosti.  ([Stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Podle klasifikace opravy vyžaduje "yumu" vrátit data zabezpečení, které CentOS nemá úprav.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

### <a name="unsupported-client-types"></a>Nepodporované klientské typy

Následující tabulka uvádí operační systémy, které nejsou podporovány:

|Operační systém  |Poznámky  |
|---------|---------|
|Klient Windows     | Klientské operační systémy (například Windows 7 a Windows 10) nejsou podporovány.        |
|Windows Server 2016 Nano Server     | Nepodporuje se.       |

### <a name="client-requirements"></a>Požadavky na klienta

#### <a name="windows"></a>Windows

Agenti Windows musí být nakonfigurován pro komunikaci se serverem WSUS nebo musí mít přístup ke službě Microsoft Update. Update Management můžete použít s nástrojem System Center Configuration Manager. Další informace o scénářích integrace najdete v tématu [integrace System Center Configuration Manager s Update managementem](oms-solution-updatemgmt-sccmintegration.md#configuration). [Agenta Windows](../azure-monitor/platform/agent-windows.md) je povinný. Agent se nainstaluje automaticky, pokud jste připojení virtuálního počítače Azure.

#### <a name="linux"></a>Linux

Pro Linux musí mít tento počítač přístup k úložišti aktualizací. Úložiště aktualizací může být privátní nebo veřejné. K interakci s Update managementem se vyžaduje protokol TLS 1.1 a TLS 1.2. Toto řešení nepodporuje agenta Log Analytics pro Linux, který je nakonfigurovaný k ukládání dat do více než jeden pracovní prostory Log Analytics.

Informace o instalaci agenta Log Analytics pro Linux a stáhněte si nejnovější verzi najdete v tématu [agenta Operations Management Suite pro Linux](https://github.com/microsoft/oms-agent-for-linux). Informace o tom, jak nainstalovat Log Analytics agenta pro Windows najdete v tématu [Operations Management Suite Agent pro Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Oprávnění

K vytváření a správě nasazení aktualizací, budete potřebovat určitá oprávnění. Další informace o těchto oprávnění najdete v tématu [přístupu podle rolí - Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Součásti řešení

Řešení se skládá z následujících prostředků. Prostředky se přidají do vašeho účtu Automation. Jsou to buď přímo připojených agentů nebo ve skupině pro správu Operations Manageru připojené.

### <a name="hybrid-worker-groups"></a>Skupiny Hybrid Worker

Když povolíte toto řešení, všechny Windows, na kterém je přímo připojený k pracovnímu prostoru Log Analytics se automaticky nakonfiguruje jako Hybrid Runbook Worker, aby podporoval runbooky, které jsou součástí tohoto řešení.

Každý počítač Windows, který je spravovaný tímto řešením je uveden v **skupiny hybridních pracovních procesů** podokně jako **skupina hybridních pracovních procesů systému** pro účet Automation. Řešení použít zásady vytváření názvů *název_hostitele FQDN_GUID*. Nelze cílit tyto skupiny se sadami runbook ve vašem účtu. Selžou, pokud se pokusíte. Tyto skupiny jsou určené pro podporu pouze do řešení pro správu.

Počítače s Windows můžete přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation pro podporu runbooků Automation, pokud používáte stejný účet pro řešení a členství ve skupině Hybrid Runbook Worker. Tato funkce přidané ve verzi 7.2.12024.0 Hybrid Runbook Worker.

### <a name="management-packs"></a>Sady Management Pack

Pokud vaší skupině pro správu System Center Operations Manageru je připojený k pracovnímu prostoru Log Analytics, nainstaluje se následující sady management Pack v nástroji Operations Manager. Tyto sady management Pack jsou také nainstalované na přímo připojené počítače s Windows, poté, co přidáte řešení. Není nutné konfigurovat ani spravovat tyto sady management Pack.

* Aktualizace Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Aktualizace sady pro správu nasazení

Další informace o způsobu aktualizace sad management Pack řešení najdete v tématu [připojení Operations Manageru k Log Analytics](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Pro systémy s agentem Operations Manageru abyste mohli plně spravovat pomocí správy aktualizací, je zapotřebí agenta aktualizovat tak, aby Microsoft Monitoring Agent. Zjistěte, jak aktualizovat agenta, najdete v článku [postup upgradu agenta nástroje Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents).

## <a name="onboard"></a>Povolení řešení Update Management

Pokud chcete začít, opravami systémů, je potřeba povolit řešení Update Management. Existuje mnoho způsobů, jak připojit počítače k Update Management. Následující jsou doporučené a podporované způsoby začlenění řešení:

* [Z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Z procházení více počítačů](automation-onboard-solutions-from-browse.md)
* [Ve svém účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [Pomocí runbooku Azure Automation](automation-onboard-solutions.md)
  
### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Ověřte, zda připojení počítače mimo Azure

Potvrďte, že jsou přímo připojené počítače nekomunikují s Log Analytics, po několika minutách můžete spustit jeden následující prohledávání protokolů.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na počítači s Windows můžete zkontrolovat následující informace, chcete-li ověřit připojení agenta k Log Analytics:

1. V Ovládacích panelech otevřete **agenta Microsoft Monitoring Agent**. Na **Azure Log Analytics** agenta na kartě se zobrazí následující zpráva: **Microsoft Monitoring Agent se úspěšně připojila k Log Analytics**.
2. Otevřete protokol událostí Windows. Přejděte na **Application and Services Logs\Operations Manager** a vyhledejte ID události 3000 a 5002 ID události ze zdroje **konektoru služby**. Tyto události značí, že počítač je zaregistrovaná s pracovním prostorem Log Analytics a přijímá konfiguraci.

Pokud agent nemůže komunikovat s Log Analytics a agent je nakonfigurován pro komunikaci s Internetem prostřednictvím brány firewall nebo proxy serveru, abyste potvrdili, že je správně nakonfigurována brána firewall nebo proxy server. Zjistěte, jak ověřit správnou konfiguraci brány firewall nebo proxy serveru, najdete v článku [konfiguraci sítě pro agenta Windows](../azure-monitor/platform/agent-windows.md) nebo [konfigurace sítě pro linuxového agenta](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Pokud jsou vaše Linuxové systémy nakonfigurován pro komunikaci pomocí serveru proxy nebo brány Log Analytics a připojujete toto řešení, aktualizace *proxy.conf* oprávnění udělit skupině omiuser udělili oprávnění ho číst pomocí Následující příkazy:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Zobrazit stav nově přidaných agentů systému Linux **aktualizované** po provedení vyhodnocení. Tento proces může trvat až 6 hodin.

Pokud chcete potvrdit, že skupiny pro správu Operations Manageru komunikuje s Log Analytics, najdete v článku [integrace Operations Manageru ověřit pomocí služby Log Analytics](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Shromažďování dat

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které podporují toto řešení:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Řešení shromažďuje informace o aktualizacích systému pro agenty Windows a poté zahájí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Řešení shromažďuje informace o aktualizacích systému od agentů systému Linux a pak zahajuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Řešení shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu.<br/>Přímé připojení z agenta Operations Manageru k Log Analytics není povinné. Data se přesměrovávají ze skupiny pro správu do pracovního prostoru Log Analytics. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Kontrola provádí dvakrát za den pro každý spravovaný počítač s Windows. Každých 15 minut se volá rozhraní Windows API dotazu na čas poslední aktualizace k určení, jestli se změnil stav. Pokud došlo ke změně stavu, zahájí se kontrola kompatibility.

Kontrola provádí každé tři hodiny pro každý spravovaný počítač s Linuxem.

Může trvat 30 minut až 6 hodin na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

## <a name="viewing-update-assessments"></a>Zobrazení posouzení aktualizací

Ve vašem účtu Automation vyberte **Update Management** zobrazíte stav svých počítačů.

Toto zobrazení poskytuje informace o počítačích, chybí aktualizace, nasazení aktualizací a plánovaná aktualizace nasazení. V **dodržování předpisů sloupec**, zobrazí se při posledním byl použit k vyhodnocení na počítači. V **aktualizovat připravenost agenta** sloupce, můžete zobrazit, pokud se stav aktualizace agenta. Pokud dochází k nějakému problému, vyberte na odkaz přejděte k řešení potíží s dokumentace, která může pomoci, že zjistíte, jaké kroky pro odstranění problému.

Spustit hledání v protokolu, který vrací informace o počítači, aktualizace nebo nasazení, vyberte položku v seznamu. **Prohledávání protokolů** pomocí dotazu pro vybranou položku se otevře podokno:

![Výchozí zobrazení Správa aktualizací](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalovat aktualizace

Po aktualizace se vyhodnocuje pro všechny systémy Linux a Windows počítače ve vašem pracovním prostoru, můžete nainstalovat požadované aktualizace vytvořením *nasazení aktualizací*. Nasazení aktualizací je plánovaná instalace požadovaných aktualizací pro jeden nebo více počítačů. Zadáte datum a čas pro nasazení a počítač nebo skupinu počítačů, které chcete zahrnout do oboru nasazení. Další informace o skupinách počítačů najdete v tématu [Skupiny počítačů v Log Analytics](../azure-monitor/platform/computer-groups.md).

 Když do svého nasazení aktualizací zahrnete skupiny počítačů, členství ve skupině se vyhodnotí jenom jednou, v době vytvoření plánu. Následné změny ve skupině se neprojeví. K orientaci použijte [dynamické skupiny](#using-dynamic-groups), tyto skupiny jsou vyřešené v době nasazení a jsou definované v dotazu.

> [!NOTE]
> Windows virtuálních počítačů nasazených z Azure Marketplace ve výchozím nastavení jsou nastaveny na přijímání automatických aktualizací ze služby Windows Update. Toto chování se při přidání tohoto řešení nebo přidat virtuální počítače Windows do pracovního prostoru nezmění. Pokud aktualizace není aktivně spravovat pomocí tohoto řešení, použije se výchozí chování (Automatické aktualizace).

Vyhněte se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci balíčku Unattended-Upgrade tak, aby automatické aktualizace. Informace o tom, jak nakonfigurovat balíček najdete v tématu [téma automatické aktualizace v příručce k Ubuntu serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Virtuální počítače vytvořené z imagí Red Hat Enterprise Linux (RHEL) na vyžádání, které jsou k dispozici na webu Azure Marketplace jsou registrované pro přístup [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) , která je nasazena v Azure. Všechny ostatní distribuce musí být aktualizované z úložiště online souborů distribuce podle podporované metody distribuce.

Chcete-li vytvořit nové nasazení aktualizace, vyberte **naplánovat nasazení aktualizací**. **Nové nasazení aktualizace** se otevře podokno. Zadejte hodnoty vlastností popsaných v následující tabulce a potom klikněte na tlačítko **vytvořit**:

| Vlastnost | Popis |
| --- | --- |
| Název |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Linux nebo Windows|
| Skupiny, které se aktualizace (preview)|Definování dotazu na základě kombinace předplatného, skupiny prostředků, míst a značky vytvářet dynamické skupiny virtuálních počítačů Azure má zahrnout do vašeho nasazení. Další informace najdete v tématu [dynamické skupiny](automation-update-management.md#using-dynamic-groups)|
| Počítače k aktualizaci |Vyberte uložená hledání, importované skupiny, nebo vybrat počítač z rozevíracího seznamu a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů.</br> Další informace o různých způsobech vytváření skupin počítačů v Log Analytics najdete v tématu [Skupiny počítačů v Log Analytics](../azure-monitor/platform/computer-groups.md). |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete|
|Zahrnout nebo vyloučit aktualizace|Tím se otevře **zahrnout/vyloučit** stránky. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o zpracování zařazení, naleznete v tématu [zahrnutí chování](automation-update-management.md#inclusion-behavior) |
|Nastavení plánu|Vyberte čas spuštění a vyberte buď jednou nebo opakovaně pro opakování|
| Předběžné skripty a pozálohovacích skriptů|Vyberte skripty spouštěné před a po nasazení|
| Časové období údržby |Počet minut pro aktualizace. Hodnota nemůže být menší než 30 minut a maximálně 6 hodin |
| Restartovat ovládacího prvku| Určuje, jak by měl být zpracována restartování počítače. Dostupné možnosti jsou:</br>Restartovat v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy nerestartovat</br>Pouze restartovat – nenainstalují se aktualizace|

Nasazení aktualizací můžete vytvořit také prostřednictvím kódu programu. Informace o vytvoření nasazení aktualizace pomocí rozhraní REST API najdete v tématu [vytvoření konfigurace aktualizace softwaru -](/rest/api/automation/softwareupdateconfigurations/create). Je také ukázkové sady runbook, který slouží k vytvoření týdenní aktualizace nasazení. Další informace o této sady runbook najdete v tématu [vytvořte týdenní nasazení aktualizací pro jeden nebo více virtuálních počítačů ve skupině prostředků](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="multi-tenant"></a>Nasazení aktualizací napříč tenanty

Pokud máte počítače v jiném tenantovi Azure, vytváření sestav správy aktualizací, které potřebujete k opravě, budete muset použít následující alternativní řešení zobrazíte je naplánované. Můžete použít [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule?view=azurermps-6.13.0) rutiny s přepínačem `-ForUpdate` vytvoření plánu a použití [AzureRmAutomationSoftwareUpdateConfiguration nový](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration?view=azurermps-6.13.0
) rutiny a předejte mu v druhém tenantovi pro počítače `-NonAzureComputer` parametru. Následující příklad ukazuje příklad o tom, jak to udělat:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Zobrazit chybějící aktualizace

Vyberte **chybějící aktualizace** zobrazíte seznam aktualizací, které nebyly nalezeny z vašich počítačů. Jednotlivé aktualizace je uvedena v seznamu a lze vybrat. Informace o počtu počítačů, které vyžadují aktualizace, operační systém a odkaz Další informace se zobrazí. **Prohledávání protokolů** podokně se zobrazí další podrobnosti o aktualizacích.

## <a name="view-update-deployments"></a>Zobrazení nasazení aktualizace

Vyberte **nasazení aktualizace** kartu, chcete-li zobrazit seznam existujících nasazení aktualizace. Vyberte některou z nasazení aktualizací v tabulce, čímž otevřete **aktualizovat spuštění nasazení** podokno pro toto nasazení aktualizace.

![Přehled výsledků nasazení aktualizace](./media/automation-update-management/update-deployment-run.png)

Nasazení aktualizací z rozhraní REST API najdete v tématu [spuštění konfigurace aktualizace softwaru](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Klasifikace aktualizací

Klasifikacích aktualizací v Update Management s definicí pro každou klasifikaci v následujících tabulkách.

### <a name="windows"></a>Windows

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace     | Aktualizaci pro určitý problém, která řeší kritickou nesouvisející zabezpečení chybu.        |
|Aktualizace zabezpečení     | Aktualizaci pro problém se související se zabezpečením určitého produktu.        |
|Kumulativní aktualizace     | Kumulativní sadu oprav hotfix spojených do balíčku pro snadné nasazení.        |
|Balíčky funkcí     | Nové funkce produktu, distribuované mimo vydání produktu.        |
|Aktualizace Service Pack     | Kumulativní sadu oprav hotfix, které se použijí pro aplikaci.        |
|Aktualizace definic     | Aktualizace virů či jiných souborů definic.        |
|Nástroje     | Nástroj nebo funkci sloužící dokončení jedné nebo několika úloh.        |
|Aktualizace     | Aktualizace aplikace nebo souboru, který je nainstalován.        |

### <a name="linux"></a>Linux

|Klasifikace  |Popis  |
|---------|---------|
|Důležité aktualizace a aktualizace zabezpečení     | Aktualizace pro určitý problém nebo problém se související se zabezpečením určitého produktu.         |
|Další aktualizace     | Všechny další aktualizace, které nejsou zásadně důležité ze své podstaty nebo nejsou aktualizace zabezpečení.        |

Pro Linux, můžete správu aktualizací rozlišovat mezi důležité a aktualizace zabezpečení v cloudu a současně zobrazením dat hodnocení z důvodu rozšiřování dat v cloudu. Pro používání dílčích oprav, správu aktualizací závisí na klasifikaci dat na počítači k dispozici. Na rozdíl od jiné distribuce CentOS není nutné tyto informace k dispozici ihned. Pokud máte počítače CentOS nakonfigurované tak, aby vrátit data zabezpečení pro následující příkaz, správu aktualizací budou moci oprava podle klasifikace.

```bash
sudo yum -q --security check-update
```

Aktuálně neexistuje žádná metoda podporovaná metoda Povolit nativní klasifikace dat dostupnost na CentOS. V tuto chvíli je podporované jenom best effort pro zákazníky, kteří mohou povolili to sami.

## <a name="firstparty-predownload"></a>První strany, použití dílčích oprav a předem stáhnout

Správa aktualizací spoléhá na webu Windows Update ke stažení a instalaci aktualizací Windows. V důsledku toho respektujeme řadu nastavení aktualizace Windows. Pokud nastavení použijete, aby povolovala aktualizace mimo Windows, správu aktualizací, spravovat a tyto aktualizace. Pokud chcete povolit stahování aktualizace, než dojde k nasazení aktualizací, nasazení aktualizací můžete začít pracovat rychleji a méně pravděpodobné překročení časového období údržby.

### <a name="pre-download-updates"></a>Náhled stahování aktualizací

Konfigurace automaticky stahování aktualizací v zásadách skupiny, můžete nastavit [nastavení konfigurace automatických aktualizací](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#BKMK_comp5) k **3**. Stáhne aktualizace potřeba na pozadí, ale neinstaluje je. To zajišťuje Správa aktualizací v ovládacím prvku plánů, ale povolit aktualizace stáhnout mimo časové období údržby Update Management. To může zabránit **překročilo se časové období údržby** chyby v Update Management.

Můžete to také nastavit pomocí prostředí PowerShell, spusťte následující příkaz Powershellu v systému, které chcete automaticky – stahování aktualizací.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="enable-updates-for-other-microsoft-products"></a>Povolit aktualizace pro ostatní produkty Microsoftu

Ve výchozím nastavení aktualizace Windows pouze poskytuje aktualizace pro Windows. Pokud povolíte **nabízet aktualizace pro ostatní produkty Microsoftu při aktualizaci Windows**, jsou součástí aktualizace ostatních produktů, včetně oprav zabezpečení takové věci pro SQL Server nebo jiný software první strany. Tuto možnost nelze konfigurovat pomocí zásad skupiny. Spusťte následující příkaz Powershellu v systémech, které chcete povolit jiné první strany opravy na a Update Management se případném dalším sdílení dodržovat tato nastavení.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a> Opravy třetích stran ve Windows

Správa aktualizací spoléhá na službu WSUS nebo Windows Update o opravu podporované systémy Windows. Nástroje, jako je [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
) (Updates Publisher) umožní publikovat vlastní aktualizace do služby WSUS. Tento scénář umožňuje správu aktualizovat opravu počítačů, které pomocí služby WSUS jako jejich úložiště aktualizací se softwarem třetích stran. Informace o konfiguraci nástroje Updates Publisher najdete v tématu [nainstalovat Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Plánování sítě

Tyto adresy jsou požadovány speciálně pro správu aktualizací. Probíhá komunikace na tyto adresy přes port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Další informace o portech, které vyžaduje, aby Hybrid Runbook Worker, naleznete v tématu [porty role Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Doporučuje se použít adresy, které uvedete při definování výjimky. Pro IP adresy, které si můžete stáhnout [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Tento soubor se každý týden aktualizuje a zobrazuje aktuálně nasazené rozsahy a všechny nadcházející změny rozsahů IP adres.

## <a name="search-logs"></a>Hledání protokolů

Kromě podrobností, které jsou k dispozici na webu Azure Portal můžete provést hledání v protokolech. Na stránce řešení vyberte **Log Analytics**. **Prohledávání protokolů** se otevře podokno.

Můžete se také dozvíte, jak upravit dotazy nebo je používat od různých klientů a víc návštěvou:  [Dokumentace k rozhraní API hledání log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Ukázkové dotazy

Ukázkové dotazy protokolu pro záznamy aktualizace shromážděné tímto řešením v následujících částech:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Dotazy na jeden posouzení virtuálních počítačů Azure (Windows)

Nahraďte hodnotu VMUUID identifikátor GUID virtuálního počítače z virtuálního počítače, který dotazujete. Můžete najít VMUUID, který se má použít spuštěním následujícího dotazu v Log Analytics: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Chybějící aktualizace souhrnu

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Posouzení dotazy jeden virtuální počítač Azure (Linux)

U některých distribucích systému Linux existuje [endianitou](https://en.wikipedia.org/wiki/Endianness) neshoda s VMUUID hodnotu, která pochází z Azure Resource Manageru a co je uložená ve službě Log Analytics. Následující dotaz vyhledává shoda s buď ukládání významných bajtů. Nahraďte hodnoty VMUUID formát big-endian a little endian formát čísla GUID správně vrátit výsledky. Můžete najít VMUUID, který se má použít spuštěním následujícího dotazu v Log Analytics: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Chybějící aktualizace souhrnu

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Dotazy posouzení více virtuálních počítačů

##### <a name="computers-summary"></a>Souhrn počítačů

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Chybějící aktualizace souhrnu

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Seznam počítačů

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Používat dynamické skupiny (preview)

Správa aktualizací umožňuje cílit na dynamické skupiny virtuálních počítačů Azure pro nasazení aktualizací. Tyto skupiny jsou definované v dotazu po zahájení nasazení aktualizací, členové této skupiny jsou vyhodnocovány. Při definování vašeho dotazu, následující položky lze použít společně a naplnit dynamickou skupinu

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Výběr skupin](./media/automation-update-management/select-groups.png)

Zobrazení náhledu na výsledky dynamické skupiny, klikněte na tlačítko **ve verzi Preview** tlačítko. Tento náhled zobrazuje členství ve skupině v tuto chvíli v tomto příkladu jsme se hledaný pro počítače se značkou **Role** rovná **BackendServer**. Pokud se tato značka přidá mají další počítače, budou přidány pro všechny budoucí nasazení pro tuto skupinu.

![skupiny ve verzi Preview](./media/automation-update-management/preview-groups.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrace se System Center Configuration Managerem

Zákazníci, kteří investovali do System Center Configuration Manager pro správu počítačů, serverů a mobilních zařízení také využívají sílu a další přednosti nástroje Configuration Manager k usnadnění správy aktualizací softwaru. Nástroj Configuration Manager je součástí jejich software cyklu správy aktualizací (SUM).

Zjistěte, jak integrovat do řešení pro správu pomocí nástroje System Center Configuration Manager, najdete v článku [integrace System Center Configuration Manager s Update managementem](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Zahrnutí chování

Zahrnutí aktualizací můžete zadat konkrétní aktualizace použít. Jsou nainstalované opravy nebo balíčky, které jsou zahrnuty. Při balíčky nebo opravy jsou součástí a klasifikaci je také vybrána, nainstaluje se zahrnuté položky i položky, které splňují klasifikace.

Je důležité vědět, že vyloučení přepsat zahrnutí. Například pokud definujete pravidla vyloučení z `*`, pak žádné opravy nebo balíčky jsou nainstalovány jako že jsou všechny vyloučené. Vyloučené opravy stále zobrazují jako chybějící z počítače. Pro počítače s Linuxem v případě, že balíček je součástí, ale má závislý balíček, který byl vyloučen, že balíček není nainstalovaný.

## <a name="patch-linux-machines"></a>Počítače s Linuxem opravy

Následující části popisují možné problémy s Linuxem opravy.

### <a name="unexpected-os-level-upgrades"></a>Neočekávané upgrady na úrovni operačního systému

Na některé varianty Linuxu, jako je Red Hat Enterprise Linux může dojít k upgradu na úrovni operačního systému prostřednictvím balíčků. Může to vést ke spuštění správy aktualizací ve kterém se mění číslo verze operačního systému. Protože Update Management používá stejné metody aktualizovat balíčky, které správce byste použili místně na počítači s Linuxem, toto chování je záměrné.

Pokud chcete vyhnout, aktualizace verze operačního systému pomocí správy aktualizací spuštění, použijte **vyloučení** funkce.

V Red Hat Enterprise Linux je název balíčku pro vyloučení redhat-release-server.x86_64.

![Balíčky pro vyloučení pro Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritické / opravy zabezpečení se nepoužijí.

Když nasazujete aktualizace do počítače s Linuxem, můžete vybrat klasifikace aktualizací. Tím se vyfiltrují aktualizace, které se použijí pro počítače, které splňují zadaná kritéria. Tento filtr je použit místně na počítači, při nasazení aktualizace.

Protože Update Management se provede aktualizace rozšíření v cloudu, některé aktualizace může být označen jako v Update Management tak, že má dopad na zabezpečení, i když v místním počítači nemá těchto informací. V důsledku toho pokud použijete důležité aktualizace pro počítač s Linuxem, může existovat aktualizace, které nejsou označeny tak, že má dopad na zabezpečení, počítači a aktualizace se nepoužijí.

Update Management však stále hlásit tohoto počítače, jako je nedodržují předpisy, protože obsahuje další informace o příslušné aktualizace.

Nasazení aktualizací podle klasifikace aktualizací nebude fungovat na CentOS úprav. Správně nasazení aktualizací pro CentOS, vyberte všechny klasifikace k zajištění, že se mají aktualizace instalovat. Pro SUSE vyberete *pouze* jiné aktualizace klasifikace může vést k některé zabezpečení aktualizuje také nainstalované Pokud aktualizace zabezpečení související s zypperu (Správce balíčků) nebo jeho závislosti jsou požadovány nejprve. Toto chování se o omezení zypperu. V některých případech je může být nutné znovu spustit nasazení aktualizace. Pokud chcete ověřit, zkontrolujte protokol aktualizace.

## <a name="remove-a-vm-for-update-management"></a>Odebrání virtuálního počítače pro správu aktualizací

Odebrání virtuálního počítače ze správy aktualizací:

* V pracovním prostoru Log Analytics, odeberte virtuální počítač z uloženého hledání pro konfiguraci oboru `MicrosoftDefaultScopeConfig-Updates`. Uložená hledání najdete v části **Obecné** ve vašem pracovním prostoru.
* Odeberte [Microsoft Monitoring agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [agenta Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).
  
## <a name="troubleshoot"></a>Řešení potíží

Zjistěte, jak řešení Update Management, najdete v článku [řešení potíží se správou aktualizací](troubleshoot/update-management.md)

## <a name="next-steps"></a>Další postup

Pokračujte ke kurzu se naučíte spravovat aktualizace pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

* Použijte hledání v protokolu [Log Analytics](../log-analytics/log-analytics-log-searches.md) k zobrazení podrobných údajů o aktualizaci.
* [Vytvořit upozornění](automation-tutorial-update-management.md#configure-alerts) stav nasazení aktualizace.

* Zjistěte, jak pracovat s Update managementem přes rozhraní REST API, najdete v článku [konfigurace aktualizace softwaru](/rest/api/automation/softwareupdateconfigurations)

