---
title: "Funkce Hybrid Runbook Worker služby Azure Automation ve Windows"
description: "Tento článek obsahuje informace o instalaci služby Azure Automation hybridní pracovní proces Runbooku umožňující spouštění sad runbook v počítačích se systémem Windows ve vaší místní datacenter nebo cloudového prostředí."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c03b124e21603056219a6ff6458469b4ecd60969
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Postup nasazení služby Windows Hybrid Runbook Worker

Runbooky ve službě Azure Automation nemají přístup k prostředkům v ostatních cloudů nebo v místním prostředí, protože spustit i v cloudu Azure.  Funkce hybridní pracovní proces Runbooku automatizace Azure umožňuje spuštění sad runbook přímo na počítači, který hostuje roli a s prostředky v prostředí pro správu těchto místních prostředků. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači.  

Tato funkce je znázorněno na následujícím obrázku:<br>

![Přehled hybridních služby Runbook Worker](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Technický přehled aspektů hybridní pracovní proces Runbooku role a nasazení, naleznete v části [přehled architektury automatizace](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Skupinám hybrid Runbook Worker

Každý hybridní pracovní proces Runbooku je členem skupiny hybridní pracovní proces Runbooku, který zadáte při instalaci agenta.  Skupina může obsahovat jednoho agenta, ale můžete nainstalovat více agentů ve skupině pro vysokou dostupnost.

Při spuštění sady runbook pro Hybrid Runbook Worker, určete skupinu, která běží na.  Členové skupiny určit, které pracovní obsluhuje požadavek.  Nelze zadat konkrétní pracovního procesu.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalace služby Windows Hybrid Runbook Worker 

Instalace a konfigurace Windows Hybrid Runbook Worker, existují dvě metody k dispozici.  Doporučený postup je použít runbook služby automatizace zcela automatizovat proces vyžadované ke konfiguraci počítači se systémem Windows.  Druhý způsob je následující podrobný postup při ručně nainstalujte a nakonfigurujte roli.  

> [!NOTE]
> Ke správě konfigurace serverů podpora roli hybridní pracovní proces Runbooku s potřeby konfigurace stavu (DSC), musíte je přidat jako uzly DSC.  Další informace o připojování je pro správu s DSC, najdete v části [registrace počítačů pro správu Azure Automation DSC](automation-dsc-onboarding.md).        
Pokud povolíte [řešení pro správu aktualizací](../operations-management-suite/oms-solution-update-management.md), jakékoli připojené k pracovní prostor OMS počítač se systémem Windows je automaticky nakonfigurovaný jako hybridní pracovní proces Runbooku na podporu sady runbook, které jsou zahrnuté v tomto řešení.  Však není registrován u žádné skupiny hybridní pracovní proces již definována v účtu Automation.  Počítač lze přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation na podporu automatizace sady runbook, dokud používáte stejný účet pro řešení a členství ve skupině hybridní pracovní proces Runbooku.  Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.  

Zkontrolujte následující informace ohledně [požadavky na hardware a software](automation-offering-get-started.md#hybrid-runbook-worker) a [informace pro přípravu síti](automation-offering-get-started.md#network-planning) než začnete nasazovat hybridní pracovní proces Runbooku.  Po úspěšném nasazení služby runbook worker, zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.  
 
### <a name="automated-deployment"></a>Automatické nasazení

Proveďte následující kroky k automatizaci instalace a konfigurace role Windows hybridní pracovní proces.  

1. Stažení *New-OnPremiseHybridWorker.ps1* skript z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/) přímo v počítači s rolí hybridní pracovní proces Runbooku nebo z jiného počítače ve vašem prostředí a zkopírujte ho do pracovního procesu.  

    *New-OnPremiseHybridWorker.ps1* skriptu během provádění vyžaduje následující parametry:

  * *AutomationAccountName* (povinné) – název účtu Automation.  
  * *Název skupiny prostředků* (povinné) - název skupiny prostředků spojené s vaším účtem Automation.  
  * *HybridGroupName* (povinné) - název skupiny hybridní pracovní proces Runbooku, který zadáte jako cíl pro sady runbook, podporuje tento scénář. 
  *  *ID předplatného* (povinné) – ID předplatného Azure, který váš účet automatizace je v.
  *  *WorkspaceName* (volitelné) – název pracovního prostoru OMS.  Pokud nemáte pracovním prostorem OMS, skript se vytvoří a nakonfiguruje jeden.  

     > [!NOTE]
     > Aktuálně jsou pouze automatizace oblasti, které podporuje integraci s OMS - **Austrálie – jihovýchod**, **východní USA 2**, **jihovýchodní Asie**, a **západní Evropa**.  Pokud svůj účet Automation není v jednom z těchto oblastí, pracovní prostor služby OMS vytvoří skript ale ho vás varuje, že ho nelze propojit je společně.
     >
2. V počítači, spusťte **prostředí Windows PowerShell** z **spustit** obrazovky v režimu správce.  
3. Z příkazového řádku prostředí PowerShell, přejděte do složky, který obsahuje skript stáhli a provést změnou hodnoty parametrů *- AutomationAccountName*, *- ResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, a *- WorkspaceName*.

     > [!NOTE] 
     > Zobrazí se výzva k ověření pomocí Azure po spuštění skriptu.  Můžete **musí** Přihlaste se pomocí účtu, který je členem role Správci předplatného a spolusprávce předplatného.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Zobrazí se výzva k souhlas k instalaci **NuGet** a zobrazí se výzva k ověření pomocí přihlašovacích údajů Azure.<br><br> ![Spuštění skriptu New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Po dokončení skriptu skupinám Hybrid Worker stránka se zobrazí nové skupiny a počet členů, nebo pokud existující skupiny, se zvýší počet členů.  Můžete vybrat skupinu ze seznamu na **skupinám Hybrid Worker** a vyberte **hybridní pracovní procesy** dlaždici.  Na **hybridní pracovní procesy** stránky, uvidíte každého člena skupiny uvedené.  

### <a name="manual-deployment"></a>Ruční nasazení 

Po provedení první dva kroky pro vaše prostředí automatizace a potom zopakujte zbývající kroky pro každý počítač pracovního procesu.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Vytvořit pracovní prostor služby Operations Management Suite

Pokud již nemáte pracovní prostor služby Operations Management Suite, vytvořte jednu pomocí pokynů v [pracovního prostoru Správa](../log-analytics/log-analytics-manage-access.md). Pokud již účet máte, můžete použít existujícímu pracovnímu prostoru.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Přidat řešení služby Automation do pracovního prostoru služby Operations Management Suite

Řešení přidání funkce do Operations Management Suite.  Řešení služby Automation přidá funkce pro Azure Automation, včetně podpory pro hybridní pracovní proces Runbooku.  Když přidáte řešení do pracovního prostoru, automaticky vynutí součásti pracovního procesu na počítači agenta, který budete instalovat v dalším kroku.

Postupujte podle pokynů v [přidat řešení pomocí Galerie řešení](../log-analytics/log-analytics-add-solutions.md) přidat **automatizace** řešení do pracovního prostoru služby Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalace agenta Microsoft Monitoring Agent

Microsoft Monitoring Agent připojí počítače k Operations Management Suite.  Při instalaci agenta na místním počítači a připojte ho do pracovního prostoru, bude automaticky stahovat komponent potřebných pro hybridní pracovní proces Runbooku.

Postupujte podle pokynů v [počítače se systémem Windows se připojit k analýze protokolů](../log-analytics/log-analytics-windows-agent.md) instalace agenta na místním počítači.  Tento proces pro více počítačů, které chcete přidat více pracovníků pro vaše prostředí, můžete opakovat.

Pokud agenta se úspěšně připojila k Operations Management Suite, objeví se na **připojené zdroje** kartě Operations Management Suite **nastavení** podokně.  Můžete ověřit, že agent správně stáhla řešení služby Automation pokud obsahuje složku s názvem **AzureAutomationFiles** v C:\Program Files\Microsoft Monitoring Agent\Agent.  Potvrďte verzi hybridní pracovní proces Runbooku, můžete přejít na C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ a Poznámka \\ *verze* podsložky.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Nainstalovat prostředí runbooku a připojte se k Azure Automation.

Když přidáte agenta Operations Management Suite, vynutí se řešení služby Automation **HybridRegistration** modulu PowerShell, který obsahuje **Add-HybridRunbookWorker** rutiny.  Tuto rutinu použijete na počítač nainstalovat prostředí runbooku a zaregistrovat ho u automatizace Azure.

Otevřete relaci prostředí PowerShell v režimu správce a spusťte následující příkazy pro import modulu.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Spusťte **Add-HybridRunbookWorker** rutiny pomocí následující syntaxe:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Můžete získat požadované informace o této rutině z **Správa klíčů** na portálu Azure.  Otevřete tuto stránku výběrem **klíče** možnost z **nastavení** stránky ve vašem účtu Automation.

![Přehled hybridních služby Runbook Worker](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** je název skupinu hybridních pracovních procesů Runbook. Pokud tato skupina již existuje v účtu automation, je aktuální počítač přidán do ní.  Pokud již neexistuje, pak se přidá.
* **Koncový bod** je **URL** pole **Správa klíčů** stránky.
* **Token** je **primární přístupový klíč** v **Správa klíčů** stránky.  

Použití **-Verbose** přepínač s **Add-HybridRunbookWorker** získat podrobné informace o instalaci.

#### <a name="5-install-powershell-modules"></a>5. Instalace modulů prostředí PowerShell

Sady Runbook můžete použít některou z aktivity a rutin, které jsou definované v modulech nainstalovaných ve vašem prostředí Azure Automation.  Tyto moduly se nenasadí automaticky pro místní počítače, když, je nutné nainstalovat ručně.  Výjimkou je modul Azure, která je nainstalována ve výchozím nastavení poskytování přístupu k rutinám pro všechny služby Azure a aktivity pro Azure Automation.

Vzhledem k tomu, že primárním účelem funkci hybridní pracovní proces Runbooku je ke správě místních prostředků, budete pravděpodobně muset nainstalovat moduly, které podporují tyto prostředky.  Můžete se podívat do [instalaci modulů](http://msdn.microsoft.com/library/dd878350.aspx) informace o instalaci moduly prostředí Windows PowerShell.  Moduly, které jsou nainstalovány musí být v umístění odkazuje proměnná prostředí PSModulePath tak, aby automaticky importují podle hybridní pracovní proces.  Další informace najdete v tématu [úprava cesta instalace PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="troubleshooting"></a>Řešení potíží 

Hybridní pracovní proces Runbooku závisí na agenta Microsoft Monitoring Agent komunikovat s vaším účtem Automation k registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, zde jsou některé možné příčiny chyby:  

1. Hybridní pracovní proces je za proxy nebo brány firewall.  
    Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na portu 443.  

2. Hybridní pracovní proces běží na počítač má menší než minimální hardwarové [požadavky](automation-offering-get-started.md#hybrid-runbook-worker).  
    Počítače se systémem hybridní pracovní proces Runbooku by měl splňovat minimální požadavky na hardware před označením ho k hostování této funkce. V závislosti na využití prostředků jiné procesy na pozadí a kolizí způsobené sady runbook během provádění, jinak hodnota počítače se stane přetížen a způsobit zpoždění úlohy sady runbook nebo vypršení časových limitů.
   Potvrďte, že počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální požadavky na hardware.  Pokud ano, sledujte využití procesoru a paměti k určení všech korelace mezi výkon procesů Hybrid Runbook Worker a Windows.  Pokud je paměť nebo zatížení procesoru, může to znamenat potřeba upgradovat nebo přidáním dalších procesorů, případně zvyšte paměti adres problémové místo prostředků a opravte případné chyby. Nebo vyberte jiný výpočtový prostředek, který může podporovat minimální požadavky a škálování při vytížení indikovat, že se o zvýšení nezbytné.
    
3. Služba Microsoft Monitoring Agent není spuštěna.  
    Jestliže není spuštěna služba Microsoft Monitoring Agent Windows, nebude hybridní pracovní proces Runbooku komunikaci se službou Azure Automation.  Ověřte agenta běží tak, že zadáte následující příkaz prostředí PowerShell: `get-service healthservice`.  Pokud je služba zastavená, zadejte následující příkaz v prostředí PowerShell spustit službu: `start-service healthservice`.  

4. V **aplikace a Správce služby Logs\Operations** protokolu událostí se zobrazí události 4502 a EventMessage obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**s následující popis: *certifikát předložený službu \<wsid\>. oms.opinsights.azure.com nevydala certifikační autorita používaná pro služby společnosti Microsoft. Obraťte se na správce sítě a zjistěte, zda používají proxy server, který zabrání komunikace TLS/SSL. V článku KB3126513 obsahuje další informace o připojení k řešení potíží.*
    Příčinou může být proxy serveru nebo síťové brány firewall blokuje komunikaci s Microsoft Azure.  Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na porty 443.

Protokoly se ukládají místně na každém hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Můžete zkontrolovat, zda existují jakékoli upozornění nebo chybové události zapsané do **aplikace a služby Logs\Microsoft-SMA\Operations** a **aplikace a Správce služby Logs\Operations** protokolu událostí, které by signalizovat připojení nebo jiné problém ovlivňující registrace role Azure Automation nebo problém při provádění operací se Normální.  

## <a name="next-steps"></a>Další kroky

* Zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.
* Pokyny o tom, jak odebrat procesy Hybrid Runbook Worker najdete v tématu [odebrat Azure automatizace procesů Hybrid Runbook Worker](automation-remove-hrw.md)