---
title: Řešení potíží s Azure Automation procesů Hybrid Runbook Worker
description: Tento článek poskytuje informace pro řešení potíží Azure Automation procesy Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679332"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Řešení potíží s procesy Hybrid Runbook Worker

Tento článek poskytuje informace o řešení potíží s procesy Hybrid Runbook Worker.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="general"></a>Obecné

Hybrid Runbook Worker závisí na agentovi ke komunikaci s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh sady Runbook a stavu sestavy. Pro Windows je tímto agentem agent Log Analytics pro Windows. Pro Linux se jedná o agenta Log Analytics pro Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scénář: spuštění sady Runbook se nezdařilo

#### <a name="issue"></a>Problém

Spuštění sady Runbook se nezdařilo a zobrazí se následující chyba.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Vaše sada Runbook je pozastavena krátce poté, co se pokusí provést tři časy. Existují podmínky, které mohou přerušit dokončení sady Runbook. Související chybová zpráva nemusí obsahovat žádné další informace.

#### <a name="cause"></a>Příčina

Níže jsou uvedené možné příčiny:

* Runbooky nelze ověřit pomocí místních prostředků.
* Hybrid Worker je za proxy serverem nebo bránou firewall.
* Počítač nakonfigurovaný na spouštění Hybrid Runbook Worker nesplňuje minimální požadavky na hardware.

#### <a name="resolution"></a>Řešení

Ověřte, zda má počítač odchozí přístup k ***. Azure-Automation.NET** na portu 443.

Počítače, na kterých běží Hybrid Runbook Worker, by měly splňovat minimální požadavky na hardware před tím, než se pracovní proces nakonfiguruje na hostování této funkce. Runbooky a procesy na pozadí, které používají, by mohly způsobit, že systém bude použit opakovaně a může dojít ke zpoždění nebo vypršení platnosti úlohy Runbooku.

Ověřte, že počítač, který spouští funkci Hybrid Runbook Worker, splňuje minimální požadavky na hardware. V takovém případě monitoruje využití procesoru a paměti k určení korelace mezi výkonem Hybrid Runbook Workerch procesů a oken. Nároky na paměť nebo procesor můžou znamenat nutnost upgradu prostředků. Můžete také vybrat jiný výpočetní prostředek, který podporuje minimální požadavky a škálování v případě požadavků na zatížení, což znamená, že je nutné zvýšit kapacitu.

V protokolu událostí **Microsoft-SMA** vyhledejte odpovídající událost s popisem `Win32 Process Exited with code [4294967295]`. Příčinou této chyby je, že v sadách Runbook jste nenakonfigurovali ověřování nebo jste určili přihlašovací údaje spustit jako pro skupinu Hybrid Runbook Worker. Zkontrolujte oprávnění sady Runbook ve [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) a ověřte, zda jste správně nakonfigurovali ověřování pro vaše Runbooky.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scénář: událost 15011 v Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Hybrid Runbook Worker přijímá událost 15011, která značí, že výsledek dotazu není platný. Následující chyba se zobrazí, když se pracovní proces pokusí otevřít připojení k [serveru signalizace](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Příčina

Hybrid Runbook Worker není správně nakonfigurovaný pro řešení automatizovaného nasazení. Toto řešení obsahuje část, která připojuje virtuální počítač k pracovnímu prostoru Log Analytics. Skript PowerShellu vyhledá pracovní prostor v předplatném se zadaným názvem. V tomto případě je Log Analytics pracovní prostor v jiném předplatném. Skript nemůže najít pracovní prostor a pokusí se ho vytvořit, ale název už je obsazený. Nasazení tak neproběhne úspěšně.

#### <a name="resolution"></a>Řešení

Tento problém můžete vyřešit dvěma způsoby:

* Upravte skript prostředí PowerShell tak, aby hledal pracovní prostor Log Analytics v jiném předplatném. Toto řešení je vhodné, pokud plánujete nasadit mnoho Hybrid Runbook Worker počítačů v budoucnu.

* Ručně nakonfigurujte pracovní počítač tak, aby běžel v izolovaném prostoru produktu Orchestrator. Pak spusťte sadu Runbook vytvořenou v účtu Azure Automation pracovního procesu a otestujte její funkčnost.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scénář: virtuální počítače Windows Azure automaticky vyřazené ze skupiny hybridních pracovních procesů

#### <a name="issue"></a>Problém

Hybrid Runbook Worker ani virtuální počítače se nezobrazí, pokud byl pracovní počítač vypnutý po dlouhou dobu.

#### <a name="cause"></a>Příčina

Hybrid Runbook Worker počítač neodpověděl Azure Automation po dobu delší než 30 dní. V důsledku toho automatizace vymazala skupinu Hybrid Runbook Worker nebo skupinu systémových pracovních procesů. 

#### <a name="resolution"></a>Řešení

Spusťte pracovní počítač a rereregister ho pomocí Azure Automation. Přečtěte si pokyny k instalaci prostředí Runbooku a připojení k Azure Automation v tématu [nasazení Hybrid Runbook Worker Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scénář: v úložišti certifikátů na Hybrid Runbook Worker nebyl nalezen žádný certifikát.

#### <a name="issue"></a>Problém

Sada Runbook spuštěná na Hybrid Runbook Worker se nezdařila s následující chybovou zprávou.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Příčina

K této chybě dochází, pokud se pokusíte použít [účet Spustit jako](../manage-runas-account.md) v sadě Runbook, která běží na Hybrid Runbook Worker, kde certifikát účtu Spustit jako není k dispozici. Procesy Hybrid Runbook Worker nemají ve výchozím nastavení prostředek certifikátu. Účet Spustit jako vyžaduje, aby tento Asset správně fungoval.

#### <a name="resolution"></a>Řešení

Pokud je vaším Hybrid Runbook Worker VIRTUÁLNÍm počítačem Azure, můžete místo toho použít [spravované identity pro prostředky Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Tento scénář zjednodušuje ověřování tím, že umožňuje ověřování prostředků Azure pomocí spravované identity virtuálního počítače Azure namísto účtu Spustit jako. Když Hybrid Runbook Worker je místní počítač, musíte na tento počítač nainstalovat certifikát účtu Spustit jako. Informace o tom, jak nainstalovat certifikát, najdete v tématu spuštění PowerShellu **Export-RunAsCertificateToHybridWorker** ve [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scénář: Chyba 403 během registrace Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Fáze prvotní registrace pracovního procesu se nezdařila a zobrazí se následující chyba (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Příčina

Níže jsou uvedené možné příčiny:

* V nastavení agenta je ID pracovního prostoru nebo klíč pracovního prostoru (primární). 

* Hybrid Runbook Worker nemůže stáhnout konfiguraci, což způsobilo chybu propojení účtu. Když Azure povolí řešení, podporuje jenom určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Je také možné, že je v počítači nastaveno nesprávné datum nebo čas. Pokud je čas +/-15 minut od aktuálního času, připojování se nepovede.

#### <a name="resolution"></a>Řešení

##### <a name="mistyped-workspace-idkey"></a>ID nebo klíč netypového pracovního prostoru
Pokud chcete ověřit, jestli se ID nebo klíč pracovního prostoru agenta nevytvořilo nesprávného typu, přečtěte si téma [Přidání nebo odebrání pracovního prostoru – agent](../../azure-monitor/platform/agent-manage.md#windows-agent) pro Windows pro agenta pro Windows nebo [Přidání nebo odebrání pracovního prostoru – agenta Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) pro agenta pro Linux.  Ujistěte se, že jste z Azure Portal vybrali úplný řetězec a pak ho pečlivě zkopírujte a vložte.

##### <a name="configuration-not-downloaded"></a>Konfigurace nebyla stažena.

Váš pracovní prostor Log Analytics a účet Automation musí být v propojené oblasti. Seznam podporovaných oblastí naleznete v tématu [Azure Automation a Log Analytics mapování pracovních prostorů](../how-to/region-mappings.md).

Může být také nutné aktualizovat datum nebo časové pásmo počítače. Pokud vyberete vlastní časový rozsah, ujistěte se, že je rozsah v UTC, což se může lišit od místního časového pásma.

## <a name="linux"></a>Linux

Hybrid Runbook Worker pro Linux závisí na [agentovi Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md) ke komunikaci s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh Runbooku a stavu sestavy. Pokud se registrace pracovního procesu nezdařila, můžete k této chybě použít některé možné příčiny:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scénář: Agent Log Analytics pro Linux není spuštěný.

#### <a name="issue"></a>Problém

Agent Log Analytics pro Linux není spuštěný.

#### <a name="cause"></a>Příčina

Pokud agent neběží, zabrání Hybrid Runbook Worker pro Linux komunikovat s Azure Automation. Je možné, že agent neběží z různých důvodů.

#### <a name="resolution"></a>Řešení

 Ověřte, že je agent spuštěný, zadáním příkazu `ps -ef | grep python`. Měl by se zobrazit výstup podobný následujícímu: aplikace Python zpracuje s uživatelským účtem **nxautomation** . Pokud řešení Update Management nebo Azure Automation není povoleno, není spuštěn žádný z následujících procesů.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

V následujícím seznamu jsou uvedeny procesy, které jsou spuštěny pro Hybrid Runbook Worker pro Linux. Všechno jsou umístěné v adresáři **/var/opt/Microsoft/omsagent/State/automationworker/** .

* **OMS. conf** – proces správce pracovních procesů. Spouští se přímo z DSC.

* **Work. conf** – automaticky registrovaný hybridní pracovní proces. Spouští ho správce pracovních procesů. Tento proces používá Update Management a je pro uživatele transparentní. Tento proces není k dispozici, pokud řešení Update Management není na počítači povoleno.

* **svépomocná/Worker. conf** – svépomocná Hybrid Worker. Hybridní pracovní proces SVÉPOMOCNÁ slouží ke spouštění sad Runbook na Hybrid Runbook Worker. Liší se pouze od automaticky registrovaného hybridního pracovního procesu v podrobnostech klíče, který používá jinou konfiguraci. Tento proces není k dispozici, pokud je řešení Azure Automation zakázané a Hybrid Worker SVÉPOMOCNÁ Linux není zaregistrované.

Pokud agent neběží, spusťte následující příkaz, který službu spustí: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scénář: Zadaná třída neexistuje.

Pokud se zobrazí chyba `The specified class does not exist..` v **/var/opt/Microsoft/omsconfig/omsconfig.log**, je nutné aktualizovat agenta Log Analytics pro Linux. Spusťte následující příkaz pro přeinstalaci agenta:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrid Runbook Worker Windows závisí na [agentu Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) , aby Windows komunikoval s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh Runbooku a stavu sestavy. V případě, že se registrace pracovního procesu nezdařila, obsahuje tato část některé z možných důvodů.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scénář: Agent Log Analytics pro Windows není spuštěný.

#### <a name="issue"></a>Problém

Na `healthservice` Hybrid Runbook Workerovém počítači není spuštěná.

#### <a name="cause"></a>Příčina

Pokud Log Analytics pro službu Windows není spuštěná, Hybrid Runbook Worker nemůže komunikovat s Azure Automation.

#### <a name="resolution"></a>Řešení

Zadáním následujícího příkazu v PowerShellu ověřte, že je agent spuštěný `Get-Service healthservice`:. Pokud je služba zastavená, zadejte do PowerShellu následující příkaz, kterým službu spustíte: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scénář: Událost 4502 v protokolu Operations Manager

#### <a name="issue"></a>Problém

V protokolu událostí **Application and Services Logs\Operations Manager** uvidíte událost 4502 a EventMessage, která obsahuje `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` následující popis:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Příčina

Důvodem může být to, že proxy server nebo brána firewall sítě blokuje komunikaci s Microsoft Azure. Ověřte, zda má počítač odchozí přístup k ***. Azure-Automation.NET** na portu 443. 

#### <a name="resolution"></a>Řešení

Protokoly se ukládají místně na každý hybridní pracovní proces na **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. V protokolech událostí **aplikace a služby Logs\Microsoft-SMA\Operations** a **Application and Services Logs\Operations Manager** můžete ověřit, zda jsou k dispozici nějaké události upozornění nebo chyby. Tyto protokoly označují připojení nebo jiný typ problému, který má vliv na zaregistrování role na Azure Automation, nebo došlo k potížím při normálním provozu. Další pomoc při řešení potíží s agentem Log Analytics najdete v tématu [řešení potíží s agentem Log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybridní pracovní procesy odesílají [výstup a zprávy Runbooku](../automation-runbook-output-and-messages.md) do Azure Automation stejným způsobem jako úlohy Runbooku běžící v cloudu odesílají výstup a zprávy. Můžete povolit streamování s podrobnými a průběhy stejně jako u runbooků.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scénář: Orchestrator. sandbox. exe se nemůže připojit k Office 365 prostřednictvím proxy

#### <a name="issue"></a>Problém

Skript, který běží na Windows Hybrid Runbook Worker, se v izolovaném prostoru (sandbox) Orchestrator nemůže připojit podle očekávání na Office 365. Skript používá pro připojení [příkaz Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) . 

Pokud upravíte soubor **Orchestrator. sandbox. exe. config** a nastavíte proxy server a seznam pro obejití, izolovaný prostor (sandbox) se stále nepřipojí správně. Zdá se, že soubor **Powershell_ise. exe. config** se stejným nastavením proxy serveru a seznamu obcházení bude fungovat podle očekávání. Protokoly Service Management Automation (SMA) a protokoly prostředí PowerShell neposkytují žádné informace týkající se proxy serveru.

#### <a name="cause"></a>Příčina

Připojení k Active Directory Federation Services (AD FS) (ADFS) na serveru nemůže obejít proxy server. Pamatujte, že izolovaný prostor (sandbox) PowerShellu běží jako přihlášený uživatel. Izolovaný prostor Orchestrator je ale silně přizpůsobený a může ignorovat nastavení souboru **Orchestrator. sandbox. exe. config** . Má speciální kód pro zpracování nastavení serveru proxy nebo MMA, ale ne pro zpracování jiných vlastních nastavení proxy serveru. 

#### <a name="resolution"></a>Řešení

Problém pro izolovaný prostor pro Orchestrator můžete vyřešit migrací skriptu tak, aby používal moduly Azure AD namísto modulu MSOnline pro rutiny prostředí PowerShell. Viz [migrace z nástroje Orchestrator na Azure Automation (beta verze)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Pokud chcete i nadále používat rutiny modulu MSOnline, změňte skript tak, aby používal [příkaz Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Zadejte hodnoty parametrů `ComputerName` a `Credential` . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Tato změna kódu spustí zcela novou relaci PowerShellu v kontextu zadaných přihlašovacích údajů. Měl by Povolit tok dat proxy server, který ověřuje aktivního uživatele.

>[!NOTE]
>Toto řešení neumožňuje manipulaci s konfiguračním souborem izolovaného prostoru (sandbox). I v případě, že jste úspěšně napracovali s vaším skriptem v konfiguračním souboru, soubor se vymaže při každém aktualizování agenta Hybrid Runbook Worker.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scénář: Hybrid Runbook Worker nevytváření sestav

#### <a name="issue"></a>Problém

Počítač s Hybrid Runbook Worker je spuštěný, ale nevidíte data prezenčního signálu pro počítač v pracovním prostoru.

Následující příklad dotazu zobrazuje počítače v pracovním prostoru a jejich poslední prezenční signál:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Příčina

Tento problém může být způsoben poškozenou mezipamětí na Hybrid Runbook Worker.

#### <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, přihlaste se k Hybrid Runbook Worker a spusťte následující skript. Tento skript zastaví agenta Log Analytics pro systém Windows, odebere jeho mezipaměť a restartuje službu. Tato akce vynutí, aby se Hybrid Runbook Worker znovu stáhla konfigurace z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scénář: nemůžete přidat Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Při pokusu o přidání Hybrid Runbook Worker pomocí `Add-HybridRunbookWorker` rutiny se zobrazí následující zpráva.

```error
Machine is already registered
```

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je počítač již zaregistrován s jiným účtem služby Automation nebo pokud se pokusíte přečíst Hybrid Runbook Worker po jeho odebrání z počítače.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, odeberte následující klíč registru, restartujte `HealthService`a znovu spusťte `Add-HybridRunbookWorker` rutinu.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Další kroky

Pokud nevidíte výše uvedený problém nebo nemůžete problém vyřešit, zkuste pro další podporu použít jeden z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)k, oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.