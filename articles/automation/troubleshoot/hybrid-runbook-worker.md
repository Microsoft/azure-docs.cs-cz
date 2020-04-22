---
title: Řešení potíží s hybridními pracovníky runbooku Azure Automation
description: Tento článek obsahuje informace pro řešení potíží s hybridními pracovníky sady Runbook Azure Automation.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679332"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Poradce při potížích s hybridními pracovníky runbooku

Tento článek obsahuje informace o řešení problémů s hybridními pracovníky runbooku.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="general"></a>Obecné

Hybridní pracovník runbooku závisí na agentovi, který bude komunikovat s vaším účtem Automation a zaregistrovat pracovníka, přijímat úlohy runbooku a vykazovat stav. Pro systém Windows je tento agent agentem Analýzy protokolů pro systém Windows. Pro Linux je to agent Log Analytics pro Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scénář: Spuštění runbooku se nezdaří

#### <a name="issue"></a>Problém

Spuštění knihy runbook se nezdaří a zobrazí se následující chyba.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook je pozastavena krátce poté, co se pokusí spustit třikrát. Existují podmínky, které mohou přerušit dokončení runbooku. Související chybová zpráva nemusí obsahovat žádné další informace.

#### <a name="cause"></a>Příčina

Možné příčiny jsou následující:

* Sady Runbook se nemohou ověřit pomocí místních prostředků.
* Hybridní pracovník je za proxy nebo firewallem.
* Počítač nakonfigurovaný pro spuštění hybridního pracovníka sady Runbook nesplňuje minimální požadavky na hardware.

#### <a name="resolution"></a>Řešení

Ověřte, zda má počítač odchozí přístup k aplikaci ***.azure-automation.net** na portu 443.

Počítače s hybridním pracovníkem sady Runbook by měly splňovat minimální požadavky na hardware před konfigurací pracovníka pro hostování této funkce. Sady Runbook a proces na pozadí, který používají, mohou způsobit nadměrné používání systému a způsobit zpoždění nebo časové výčasové lhůty úloh sady Runbook.

Zkontrolujte, zda počítač, který má spustit funkci Hybridní modul runbook worker, splňuje minimální požadavky na hardware. Pokud ano, monitorování využití procesoru a paměti k určení jakékoli korelace mezi výkonem procesů hybridní hospo- šití runbook pracovního procesu a systému Windows. Jakýkoli tlak paměti nebo procesoru může znamenat potřebu upgradovat prostředky. Můžete také vybrat jiný výpočetní prostředek, který podporuje minimální požadavky a škálování, když požadavky na zatížení označují, že zvýšení je nezbytné.

Zkontrolujte protokol událostí **Microsoft-SMA** pro `Win32 Process Exited with code [4294967295]`odpovídající událost s popisem . Příčinou této chyby je, že jste nenakonfigurovali ověřování v sadách Runbook ani nezadali pověření Spustit jako pro skupinu Hybridní pracovní proces sady Runbook. Zkontrolujte oprávnění sady Runbook v [spouštění runbooků na pracovníka hybridní sady Runbook](../automation-hrw-run-runbooks.md) a potvrďte, že jste správně nakonfigurovali ověřování pro sady Runbook.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scénář: Událost 15011 v hybridním pracovníkovi runbooku

#### <a name="issue"></a>Problém

Pracovník hybridního runbooku obdrží událost 15011, což znamená, že výsledek dotazu není platný. Následující chyba se zobrazí, když se pracovník pokusí otevřít připojení k [serveru SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Příčina

Hybridní pracovník runbooku nebyl správně nakonfigurován pro řešení automatického nasazení. Toto řešení obsahuje část, která spojuje virtuální ho s pracovním prostorem Analýzy protokolů. Skript Prostředí PowerShell hledá pracovní prostor v předplatném s zadaným názvem. V takovém případě je pracovní prostor Analýzy protokolů v jiném předplatném. Skript nemůže najít pracovní prostor a pokusí se jej vytvořit, ale název je již přijat. Proto se nasazení nezdaří.

#### <a name="resolution"></a>Řešení

Máte dvě možnosti řešení tohoto problému:

* Upravte skript Prostředí PowerShell a vyhledejte pracovní prostor Log Analytics v jiném předplatném. To je dobré řešení, pokud plánujete nasadit mnoho počítačů hybridní runbook worker v budoucnu.

* Ručně nakonfigurujte pracovní počítač tak, aby běžel v izolovaném prostoru Orchestrátoru. Pak spusťte runbook vytvořený v účtu Azure Automation na pracovníka k testování funkce.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scénář: Virtuální počítače Windows Azure automaticky vypadly z hybridní pracovní skupiny

#### <a name="issue"></a>Problém

Hybridní pracovník runbooku nebo virtuální počítače se nezobrazí, když byl pracovní počítač delší dobu vypnutý.

#### <a name="cause"></a>Příčina

Počítač Hybrid Runbook Worker nenapájí azure automation už víc než 30 dní. V důsledku toho automatizace vymazala skupinu Hybridní pracovní kniha runbooknebo skupinu Pracovník systému. 

#### <a name="resolution"></a>Řešení

Spusťte pracovní počítač a pak ho znovu zaregistrujte pomocí Azure Automation. Přečtěte si pokyny k instalaci prostředí sady Runbook a připojení k Azure Automation v [tématu Nasazení pracovníka hybridní sady Runbook pro Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scénář: V úložišti certifikátů v hybridním pracovníkovi runbooku nebyl nalezen žádný certifikát.

#### <a name="issue"></a>Problém

Spuštění runbooku spuštěné v hybridním pracovníkovi runbooku se nezdaří s následující chybovou zprávou.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Příčina

K této chybě dochází, když se pokusíte použít [účet Spustit jako](../manage-runas-account.md) v runbooku, který běží na hybridní množiny runbooku, kde není k dispozici certifikát spustit jako účet. Hybridní pracovníci runbooku nemají ve výchozím nastavení datový zdroj certifikátu místně. Účet Spustit jako vyžaduje, aby tento prostředek fungoval správně.

#### <a name="resolution"></a>Řešení

Pokud je váš hybridní runbook worker virtuální počítač Azure, můžete místo toho použít [spravované identity pro prostředky Azure.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) Tento scénář zjednodušuje ověřování tím, že umožňuje ověření prostředků Azure pomocí spravované identity virtuálního počítače Azure namísto účtu Spustit jako. Pokud je hybridní pracovník sady Runbook místní počítač, je třeba nainstalovat certifikát účtu Spustit jako do počítače. Informace o instalaci certifikátu naleznete v postupech spuštění sady Runbook PowerShell **Export-RunAsCertificateToHybridWorker** ve [spuštěných runbookech na hybridním pracovníkovi sady Runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scénář: Chyba 403 při registraci hybridního pracovníka runbooku

#### <a name="issue"></a>Problém

Počáteční fáze registrace pracovníka se nezdaří a zobrazí se následující chyba (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Příčina

Možné příčiny jsou následující:

* V nastavení agenta je chybně zadané ID pracovního prostoru nebo klíč pracovního prostoru (primární). 

* Hybridní pracovník runbooku nemůže stáhnout konfiguraci, což způsobuje chybu propojení účtu. Když Azure umožňuje řešení, podporuje pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Je také možné, že je v počítači nastaveno nesprávné datum nebo čas. Pokud je čas +/-15 minut od aktuálního času, onboarding se nezdaří.

#### <a name="resolution"></a>Řešení

##### <a name="mistyped-workspace-idkey"></a>Chybně zadané ID/klíč pracovního prostoru
Pokud chcete ověřit, jestli id pracovního prostoru agenta nebo klíč pracovního prostoru byl chybně zadán, přečtěte si informace o [přidání nebo odebrání pracovního prostoru – agenta Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) pro agenta Windows nebo Přidání nebo odebrání [pracovního prostoru – linuxového agenta](../../azure-monitor/platform/agent-manage.md#linux-agent) pro agenta Linuxu.  Ujistěte se, že vyberete celý řetězec z portálu Azure a zkopírujte ho a pečlivě ho vložte.

##### <a name="configuration-not-downloaded"></a>Konfigurace nebyla stažena.

Váš pracovní prostor Analýzy protokolů a účet automatizace musí být v propojené oblasti. Seznam podporovaných oblastí najdete v [tématu Azure Automation a Log Analytics mapování pracovního prostoru](../how-to/region-mappings.md).

Může být také nutné aktualizovat datum nebo časové pásmo počítače. Pokud vyberete vlastní časový rozsah, ujistěte se, že rozsah je v UTC, které se mohou lišit od místního časového pásma.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker závisí na [agenta Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md) komunikovat s účtem Automation zaregistrovat pracovníka, přijímat úlohy runbooku a stav sestavy. Pokud registrace pracovníka selže, zde jsou některé možné příčiny chyby:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scénář: Agent analýzy protokolů pro Linux není spuštěn

#### <a name="issue"></a>Problém

Agent Log Analytics pro Linux neběží

#### <a name="cause"></a>Příčina

Pokud agent není spuštěn, zabrání Linux hybridní Runbook Worker z komunikace s Azure Automation. Agent pravděpodobně není spuštěn z různých důvodů.

#### <a name="resolution"></a>Řešení

 Ověřte, zda je `ps -ef | grep python`agent spuštěn zadáním příkazu . Měli byste vidět výstup podobný následujícímu, procesy Pythonu s uživatelským účtem **nxautomation.** Pokud není povoleno řešení Správa aktualizací nebo Azure Automation, neběží žádný z následujících procesů.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Následující seznam ukazuje procesy, které jsou spuštěny pro Linux Hybrid Runbook Worker. Všechny jsou umístěny v adresáři **/var/opt/microsoft/omsagent/state/automationworker/** .

* **oms.conf** - Proces vedoucího pracovníka. Začalo to přímo z DSC.

* **worker.conf** - Automaticky registrovaný hybridní pracovní proces. Začalo to vedoucím pracovníka. Tento proces je používán službou Správa aktualizací a je pro uživatele transparentní. Tento proces není k dispozici, pokud není v počítači povoleno řešení správy aktualizací.

* **diy/worker.conf** - DIY hybridní pracovní proces. DIY hybridní pracovní proces se používá ke spuštění uživatelské runbooky na hybridní runbook worker. Pouze se liší od automaticky registrovaných hybridní pracovní proces v detailu klíče, který používá jinou konfiguraci. Tento proces není k dispozici, pokud je zakázáno řešení Azure Automation a DIY Linux Hybridní worker není registrován.

Pokud agent není spuštěn, spusťte službu následujícím `sudo /opt/microsoft/omsagent/bin/service_control restart`příkazem: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scénář: Zadaná třída neexistuje.

Pokud se zobrazí `The specified class does not exist..` chyba v **souboru /var/opt/microsoft/omsconfig/omsconfig.log**, je třeba aktualizovat agenta Log Analytics pro Linux. Chcete-li přeinstalovat agenta, spusťte následující příkaz:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Pracovník hybridního runbooku systému Windows závisí na [agentovi Analýzy protokolů pro systém Windows,](../../azure-monitor/platform/log-analytics-agent.md) který bude komunikovat s vaším účtem Automation a zaregistrovat pracovníka, přijímat úlohy runbooku a vykazovat stav. Pokud registrace pracovníka selže, tato část obsahuje některé možné důvody.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scénář: Agent analýzy protokolů pro systém Windows není spuštěn

#### <a name="issue"></a>Problém

Není `healthservice` spuštěn v počítači hybridní runbook pracovního automatu.

#### <a name="cause"></a>Příčina

Pokud služba Log Analytics pro Windows není spuštěná, hybridní pracovník runbooku nemůže komunikovat s Azure Automation.

#### <a name="resolution"></a>Řešení

Ověřte, zda je agent spuštěn, zadáním následujícího příkazu v prostředí PowerShell: `Get-Service healthservice`. Pokud je služba zastavena, zadejte v prostředí PowerShell následující příkaz pro spuštění služby: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scénář: Událost 4502 v protokolu nástroje Operations Manager

#### <a name="issue"></a>Problém

V **protokolu událostí Application and Services Logs\Operations Manager** se zobrazí událost `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` 4502 a EventMessage, která obsahuje následující popis:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Příčina

Tento problém může být způsoben tím, že proxy nebo síťová brána firewall blokuje komunikaci s Microsoft Azure. Ověřte, zda má počítač odchozí přístup k aplikaci ***.azure-automation.net** na portu 443. 

#### <a name="resolution"></a>Řešení

Protokoly jsou uloženy místně u každého hybridního pracovníka v **c:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. V **protokolech událostí aplikace a služeb\Microsoft-SMA\Operations** and Application and **Services Logs\Operations and Services Logs\Operations Manager logs\Operations Manager** lze ověřit, zda existují nějaké události s upozorněním nebo chybami. Tyto protokoly označují připojení nebo jiný typ problému, který ovlivňuje připojení role azure automatizace nebo problém došlo za běžných operací. Další nápovědu k řešení problémů s agentem Log Analytics najdete v [tématu Řešení problémů s agentem windows analýzy protokolů](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybridní pracovníci odesílají [výstup runbooku a zprávy](../automation-runbook-output-and-messages.md) do Azure Automation stejným způsobem, jakým úlohy runbooku spuštěné v cloudu odesílají výstup a zprávy. Můžete povolit datové proudy Verbose a Progress stejně jako pro runbooky.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scénář: Orchestrator.Sandbox.exe se nemůže připojit k Office 365 přes proxy server

#### <a name="issue"></a>Problém

Skript spuštěný v pracovníkovi hybridního runbooku Windows se nemůže připojit podle očekávání k Office 365 na izolovaném prostoru Orchestrator. Skript používá [connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) pro připojení. 

Pokud upravíte **Orchestrator.Sandbox.exe.config** nastavit proxy a seznam bypass, izolovaného prostoru stále nepřipojí správně. Zdá se, že soubor **Powershell_ise.exe.config** se stejným nastavením seznamu proxy a seznamu bypass funguje podle očekávání. Protokoly automatizace správy služeb (SMA) a protokoly prostředí PowerShell neposkytují žádné informace týkající se serveru proxy.

#### <a name="cause"></a>Příčina

Připojení ke službě ADFS služby ADFS služby ADFNemůže obejít proxy server. Nezapomeňte, že izolovaného prostoru prostředí PowerShell běží jako přihlášený uživatel. Izolovaného prostoru Orchestrator je však silně přizpůsobena a může ignorovat nastavení souboru **Orchestrator.Sandbox.exe.config.** Má speciální kód pro zpracování nastavení počítače nebo MMA proxy, ale ne pro zpracování dalších vlastních nastavení proxy serveru. 

#### <a name="resolution"></a>Řešení

Problém izolovaného prostoru Orchestrator můžete vyřešit migrací skriptu tak, aby místo modulu MSOnline pro rutiny prostředí PowerShell používal moduly Azure AD. Viz [Migrace z Orchestrator do Azure Automation (Beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Pokud chcete pokračovat v používání rutin modulu MSOnline, změňte skript tak, aby používal [příkaz Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Zadejte hodnoty `ComputerName` `Credential` parametrů a. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Tato změna kódu spustí zcela novou relaci prostředí PowerShell v kontextu zadaných pověření. Měl by umožnit přenos toku přes proxy server, který ověřuje aktivního uživatele.

>[!NOTE]
>Toto řešení znemožňuje manipulaci s konfiguračním souborem izolovaného prostoru. I v případě, že se vám podaří vytvořit konfigurační soubor pracovat se skriptem, soubor dostane vymazány pokaždé, když hybridní runbook worker agent je aktualizován.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scénář: Hybridní pracovník runbooku se nehlásí

#### <a name="issue"></a>Problém

Váš hybridní počítač Runbook Worker je spuštěn, ale nevidíte data prezenčního signálu pro počítač v pracovním prostoru.

Následující příklad dotazu ukazuje počítače v pracovním prostoru a jejich poslední prezenční signál:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Příčina

Tento problém může být způsoben poškozenou mezipamětí na hybridní pracovník runbook.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, přihlaste se k hybridnímu pracovníkovi runbooku a spusťte následující skript. Tento skript zastaví agenta Analýzy protokolů pro systém Windows, odebere jeho mezipaměť a restartuje službu. Tato akce vynutí hybridní runbook worker znovu stáhnout jeho konfiguraci z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scénář: Nelze přidat hybridní runbook worker

#### <a name="issue"></a>Problém

Při pokusu o přidání hybridního pracovníka runbooku pomocí rutiny `Add-HybridRunbookWorker` se zobrazí následující zpráva.

```error
Machine is already registered
```

#### <a name="cause"></a>Příčina

Tento problém může být způsoben, pokud je počítač již registrován s jiným účtem automatizace nebo pokud se pokusíte číst hybridní runbook worker po odebrání z počítače.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, odeberte následující klíč registru, restartujte rutinu `HealthService`a opakujte akciovou rutinu. `Add-HybridRunbookWorker`

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte výše nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.