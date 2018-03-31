---
title: Procesy služby Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o instalaci a použití hybridní pracovní proces Runbooku, což je funkce Azure Automation, který umožňuje spuštění sad runbook na počítačích ve vaší místní datacenter nebo poskytovatele cloudových služeb.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/21/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b317a2d9241016b66651af4659c7daf2e8d8f2cc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatizaci prostředků v datovém centru nebo v cloudu s hybridní pracovní proces Runbooku

Runbooky ve službě Azure Automation nemají přístup k prostředkům v ostatních cloudů nebo v místním prostředí, protože spustit i v cloudu Azure. Funkce hybridní pracovní proces Runbooku automatizace Azure umožňuje spuštění sad runbook přímo na počítači, který hostuje roli a s prostředky v prostředí pro správu těchto místních prostředků. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači.

Tato funkce je znázorněno na následujícím obrázku:

![Přehled hybridních služby Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Technický přehled aspektů hybridní pracovní proces Runbooku role a nasazení, naleznete v části [přehled architektury automatizace](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Skupinám hybrid Runbook Worker

Každý hybridní pracovní proces Runbooku je členem skupiny hybridní pracovní proces Runbooku, který zadáte při instalaci agenta. Skupina může obsahovat jednoho agenta, ale můžete nainstalovat více agentů ve skupině pro vysokou dostupnost.

Při spuštění sady runbook pro Hybrid Runbook Worker, určete skupinu, která běží na. Členové skupiny určit, které pracovní obsluhuje požadavek. Nelze zadat konkrétní pracovního procesu.

## <a name="relationship-to-service-management-automation"></a>Vztah k službě Service Management Automation

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) vám umožní spustit stejné sady runbook, které podporuje Azure Automation v místním datovém centru. SMA se nasazuje společně s Windows Azure Pack, protože Windows Azure Pack obsahuje grafické rozhraní pro správu SMA. Na rozdíl od Azure Automation SMA vyžaduje místní instalaci, která zahrnuje webových serverů k hostování rozhraní API, databáze tak, aby obsahovala sady runbook a SMA konfigurace a pracovní procesy Runbook Worker k provedení úlohy sady runbook. Automatizace Azure poskytuje tyto služby v cloudu a vyžaduje pouze, abyste mohli Udržovat procesy Hybrid Runbook Worker ve vašem místním prostředí.

Pokud jste stávajícího uživatele SMA, můžete přesunout runbooky Azure Automation, který se má použít s hybridní pracovní proces Runbooku s žádné změny, za předpokladu, že fungují své vlastní ověřování k prostředkům, jako jsou popsané v [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md). Runbooky ve službě SMA spustit v kontextu účtu služby na serveru pracovního procesu, který může poskytnout, ověření pro sady runbook.

Můžete použít následující kritéria k určení, zda je vhodnější pro své požadavky na Azure Automation Hybrid Runbook Worker nebo Service Management Automation.

* SMA vyžaduje místní instalaci jeho základní komponenty, které jsou připojené k Windows Azure Pack, pokud je potřeba doplnil o grafické rozhraní. Další místní prostředky, je potřeba s vyšší náklady na údržbu než Azure Automation, který potřebuje pouze agenta nainstalovaného na místní runbook Worker. Agenti jsou spravovány Azure, další snižují náklady na údržbu.
* Služby Azure Automation ukládá jeho runbooky v cloudu a předá je do místní hybridní pracovní procesy Runbooku. Pokud vaše zásady zabezpečení nedovolují toto chování, měli byste použít SMA.
* SMA se dodává s nástrojem System Center; a proto vyžaduje licenci System Center 2012 R2. Služby Azure Automation je založen na modelu vrstvené předplatného.
* Automatizace Azure má pokročilé funkce jako je například grafické runbooky, které nejsou k dispozici ve službě SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalace služby Windows Hybrid Runbook Worker

Instalace a konfigurace Windows Hybrid Runbook Worker, existují dvě metody k dispozici. Doporučený postup je použít runbook služby automatizace zcela automatizovat proces vyžadované ke konfiguraci počítači se systémem Windows. Druhý způsob je následující podrobný postup při ručně nainstalujte a nakonfigurujte roli.

> [!NOTE]
> Ke správě konfigurace serverů podpora roli hybridní pracovní proces Runbooku s potřeby konfigurace stavu (DSC), musíte je přidat jako uzly DSC. Další informace o připojování je pro správu s DSC, najdete v části [registrace počítačů pro správu Azure Automation DSC](automation-dsc-onboarding.md).
>
>Pokud povolíte [řešení pro správu aktualizací](../operations-management-suite/oms-solution-update-management.md), jakékoli připojené k pracovní prostor analýzy protokolů počítač se systémem Windows je automaticky nakonfigurovaný jako hybridní pracovní proces Runbooku na podporu sady runbook, které jsou zahrnuté v tomto řešení. Však není registrován u žádné skupiny hybridní pracovní proces již definována v účtu Automation. Počítač lze přidat do skupiny Hybrid Runbook Worker ve vašem účtu Automation na podporu automatizace sady runbook, dokud používáte stejný účet pro řešení a členství ve skupině hybridní pracovní proces Runbooku. Tuto funkci jsme do funkce Hybrid Runbook Worker přidali ve verzi 7.2.12024.0.

Zkontrolujte následující informace ohledně [požadavky na hardware a software](automation-offering-get-started.md#hybrid-runbook-worker) a [informace pro přípravu síti](automation-offering-get-started.md#network-planning) než začnete nasazovat hybridní pracovní proces Runbooku. Po úspěšném nasazení služby runbook worker, zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.

### <a name="automated-deployment"></a>Automatické nasazení

Proveďte následující kroky k automatizaci instalace a konfigurace role Windows hybridní pracovní proces.

1. Stažení *New-OnPremiseHybridWorker.ps1* skript z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) přímo v počítači s rolí hybridní pracovní proces Runbooku nebo z jiného počítače ve vašem prostředí a zkopírujte ho do pracovního procesu.

   *New-OnPremiseHybridWorker.ps1* skriptu během provádění vyžaduje následující parametry:

   * *AutomationAccountName* (povinné) – název účtu Automation.
   * *AAResourceGroupName* (povinné) - název skupiny prostředků spojené s vaším účtem Automation.
   * *OMSResourceGroupName* (volitelné) – název skupiny prostředků pro pracovní prostor OMS. Pokud není zadaný, použije se AAResourceGroupName.
   * *HybridGroupName* (povinné) - název skupiny hybridní pracovní proces Runbooku, který zadáte jako cíl pro sady runbook, podporuje tento scénář.
   * *ID předplatného* (povinné) – ID předplatného Azure, který váš účet automatizace je v.
   * *WorkspaceName* (volitelné) – název pracovního prostoru analýzy protokolů. Pokud nemáte pracovní prostor analýzy protokolů, skript se vytvoří a nakonfiguruje jeden.

     > [!NOTE]
     > Aktuálně jsou pouze automatizace oblasti, které podporuje integraci s analýzy protokolů - **Austrálie – jihovýchod**, **východní USA 2**, **jihovýchodní Asie**, a  **Západní Evropa**. Pokud není váš účet Automation v jednom z těchto oblastí, skript vytvoří pracovní prostor analýzy protokolů ale ho vás varuje, že ho nelze propojit je společně.

2. V počítači, spusťte **prostředí Windows PowerShell** z **spustit** obrazovky v režimu správce.
3. Z příkazového řádku prostředí PowerShell, přejděte do složky, který obsahuje skript stáhli a provést změnou hodnoty parametrů *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, a *- WorkspaceName*.

     > [!NOTE]
     > Zobrazí se výzva k ověření pomocí Azure po spuštění skriptu. Můžete **musí** Přihlaste se pomocí účtu, který je členem role Správci předplatného a spolusprávce předplatného.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Zobrazí se výzva k souhlas k instalaci **NuGet** a zobrazí se výzva k ověření pomocí přihlašovacích údajů Azure.

5. Po dokončení skriptu skupinám Hybrid Worker okna se zobrazí nové skupiny a počet členů, nebo pokud existující skupiny, se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupinám Hybrid Worker** a vyberte **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** okně uvidíte každého člena skupiny uvedené.

### <a name="manual-deployment"></a>Ruční nasazení

Po provedení první dva kroky pro vaše prostředí automatizace a potom zopakujte zbývající kroky pro každý počítač pracovního procesu.

#### <a name="1-create-log-analytics-workspace"></a>1. Vytvoření pracovního prostoru Log Analytics
Pokud již nemáte pracovní prostor analýzy protokolů, vytvořte jednu pomocí pokynů v [pracovního prostoru Správa](../log-analytics/log-analytics-manage-access.md). Pokud již účet máte, můžete použít existujícímu pracovnímu prostoru.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Přidat řešení služby Automation do pracovního prostoru analýzy protokolů

Řešení do služby Log Analytics přidávají funkčnost. Řešení služby Automation přidá funkce pro Azure Automation, včetně podpory pro hybridní pracovní proces Runbooku. Když přidáte řešení do pracovního prostoru, automaticky vynutí součásti pracovního procesu na počítači agenta, který budete instalovat v dalším kroku.

Postupujte podle pokynů v [přidat řešení pomocí Galerie řešení](../log-analytics/log-analytics-add-solutions.md) přidat **automatizace** řešení do pracovního prostoru analýzy protokolů.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalace agenta Microsoft Monitoring Agent
Microsoft Monitoring Agent připojí počítače k analýze protokolů. Při instalaci agenta na místním počítači a připojte ho do pracovního prostoru, bude automaticky stahovat komponent potřebných pro hybridní pracovní proces Runbooku.

Postupujte podle pokynů v [počítače se systémem Windows se připojit k analýze protokolů](../log-analytics/log-analytics-windows-agent.md) instalace agenta na místním počítači. Tento proces pro více počítačů, které chcete přidat více pracovníků pro vaše prostředí, můžete opakovat.

Můžete ověřit, že agent správně stáhla řešení služby Automation pokud obsahuje složku s názvem **AzureAutomationFiles** v C:\Program Files\Microsoft Monitoring Agent\Agent. Potvrďte verzi hybridní pracovní proces Runbooku, můžete přejít na C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ a Poznámka \\ *verze* podsložky.  

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Nainstalovat prostředí runbooku a připojte se k Azure Automation.

Když přidáte agenta k analýze protokolů, vynutí se řešení služby Automation **HybridRegistration** modulu PowerShell, který obsahuje **Add-HybridRunbookWorker** rutiny. Tuto rutinu použijete na počítač nainstalovat prostředí runbooku a zaregistrovat ho u automatizace Azure.

Naimportujte modul, otevřete relaci prostředí PowerShell v režimu správce a spusťte následující příkazy:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Spusťte **Add-HybridRunbookWorker** rutiny pomocí následující syntaxe:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Můžete získat informace požadované pro tuto rutinu kliknutím **klíče** možnost pod **nastavení účtu** ve vašem účtu Automation.

* **GroupName** je název skupinu hybridních pracovních procesů Runbook. Pokud tato skupina již existuje v účtu automation, je aktuální počítač přidán do ní. Pokud již neexistuje, pak se přidá.
* **Koncový bod** je **URL** pole **klíče** stránky.
* **Token** je **primární přístupový klíč** v **klíče** stránky.

Použití **-Verbose** přepínač s **Add-HybridRunbookWorker** získat podrobné informace o instalaci.

#### <a name="5-install-powershell-modules"></a>5. Instalace modulů prostředí PowerShell

Sady Runbook můžete použít některou z aktivity a rutin, které jsou definované v modulech nainstalovaných ve vašem prostředí Azure Automation. Tyto moduly se nenasadí automaticky pro místní počítače, když, je nutné nainstalovat ručně. Výjimkou je modul Azure, která je nainstalována ve výchozím nastavení poskytování přístupu k rutinám pro všechny služby Azure a aktivity pro Azure Automation.

Vzhledem k tomu, že primárním účelem funkci hybridní pracovní proces Runbooku je ke správě místních prostředků, budete pravděpodobně muset nainstalovat moduly, které podporují tyto prostředky. Můžete se podívat do [instalaci modulů](http://msdn.microsoft.com/library/dd878350.aspx) informace o instalaci moduly prostředí Windows PowerShell. Moduly, které jsou nainstalovány musí být v umístění odkazuje proměnná prostředí PSModulePath tak, aby automaticky importují podle hybridní pracovní proces. Další informace najdete v tématu [úprava cesta instalace PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="removing-hybrid-runbook-worker"></a>Odebrání hybridní pracovní proces Runbooku

Jeden nebo více procesy Hybrid Runbook Worker můžete odebrat ze skupiny nebo můžete odebrat skupiny, v závislosti na vaše požadavky. K odebrání hybridní pracovní proces Runbooku místním počítači, proveďte následující kroky:

1. Na portálu Azure přejděte na svůj účet Automation.
2. Z **nastavení** vyberte **klíče** a poznamenejte si hodnoty pro pole **URL** a **primární přístupový klíč**. Tyto informace jsou potřeba pro další krok.
3. Otevřete relaci prostředí PowerShell v režimu správce a spusťte následující příkaz: `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`. Použití **-Verbose** přepínač pro podrobný protokol proces odebrání.

> [!NOTE]
> Microsoft Monitoring Agent nebude odstraněn z počítače, funkce a konfiguraci role hybridní pracovní proces Runbooku.

## <a name="remove-hybrid-worker-groups"></a>Odebrat skupiny hybridní pracovní proces

Odebrat skupinu, je nutné nejprve odebrat hybridní pracovní proces Runbooku z každý počítač, který je členem skupiny postupem uvedena výše a potom proveďte následující kroky k odebrání skupiny.

1. Otevřete účet Automation na portálu Azure.
1. V části **automatizace procesu**, vyberte **skupinám Hybrid worker**. Vyberte skupinu, kterou chcete odstranit. Po výběru konkrétní skupinu **skupinu hybridních pracovních procesů** zobrazí se okno Vlastnosti.

   ![Skupina hybridních pracovních procesů Runbook okno](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. V okně Vlastnosti pro vybranou skupinu, klikněte na **odstranit**. Zobrazí se zpráva s žádostí o potvrzení této akce vyberte **Ano** Opravdu chcete pokračovat.

   ![Dialogové okno potvrzení odstranění skupiny](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Dokončení tohoto procesu může trvat několik sekund a průběh zpracování můžete sledovat prostřednictvím možnosti nabídky **Oznámení**.

## <a name="troubleshooting"></a>Řešení potíží

Hybridní pracovní proces Runbooku závisí na agenta Microsoft Monitoring Agent komunikovat s vaším účtem Automation k registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, zde jsou některé možné příčiny chyby:

1. Hybridní pracovní proces je za proxy nebo brány firewall.

   Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na portu 443.

2. Hybridní pracovní proces běží na počítač má menší než minimální hardwarové [požadavky](automation-offering-get-started.md#hybrid-runbook-worker).

   Počítače se systémem hybridní pracovní proces Runbooku by měl splňovat minimální požadavky na hardware před označením ho k hostování této funkce. V závislosti na využití prostředků jiné procesy na pozadí a kolizí způsobené sady runbook během provádění, jinak počítač stane přetížen a způsobit zpoždění úlohy sady runbook nebo vypršení časových limitů.

   Potvrďte, že počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální požadavky na hardware. Pokud ano, sledujte využití procesoru a paměti k určení všech korelace mezi výkon procesů Hybrid Runbook Worker a Windows. Pokud je paměť nebo zatížení procesoru, může to znamenat potřeba upgradovat nebo přidáním dalších procesorů, případně zvyšte paměti adres problémové místo prostředků a opravte případné chyby. Nebo vyberte jiný výpočtový prostředek, který může podporovat minimální požadavky a škálování při vytížení indikovat, že se o zvýšení nezbytné.

3. Služba Microsoft Monitoring Agent není spuštěna.

   Jestliže není spuštěna služba Microsoft Monitoring Agent Windows, nebude hybridní pracovní proces Runbooku komunikaci se službou Azure Automation. Ověřte agenta běží tak, že zadáte následující příkaz prostředí PowerShell: `get-service healthservice`. Pokud je služba zastavená, zadejte následující příkaz v prostředí PowerShell spustit službu: `start-service healthservice`.

4. V **aplikace a Správce služby Logs\Operations** protokolu událostí se zobrazí události 4502 a EventMessage obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**s následující popis: *certifikát předložený službu \<wsid\>. oms.opinsights.azure.com nevydala certifikační autorita používaná pro služby společnosti Microsoft. Obraťte se na správce sítě a zjistěte, zda používají proxy server, který zabrání komunikace TLS/SSL. V článku KB3126513 obsahuje další informace o připojení k řešení potíží.*
    Příčinou může být proxy serveru nebo síťové brány firewall blokuje komunikaci s Microsoft Azure. Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na porty 443.

Protokoly se ukládají místně na každém hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Můžete zkontrolovat, zda existují jakékoli upozornění nebo chybové události zapsané do **aplikace a služby Logs\Microsoft-SMA\Operations** a **aplikace a Správce služby Logs\Operations** protokolu událostí, které by signalizovat připojení nebo jiné problém ovlivňující registrace role Azure Automation nebo problém při provádění operací se Normální.

## <a name="next-steps"></a>Další postup

Zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.
