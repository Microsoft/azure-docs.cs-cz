---
title: Řešení potíží s Azure Automation Hybrid Runbook Worker
description: V tomto článku se dozvíte, jak řešit problémy, které vznikají Azure Automation procesy Hybrid Runbook Worker.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 15a18cbfc3a80bbfea0b92e5b616104dc0f593af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580991"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Řešení problémů s funkcí Hybrid Runbook Worker

Tento článek poskytuje informace o řešení potíží a řešení problémů s Azure Automation procesy Hybrid Runbook Worker. Obecné informace najdete v tématu [přehled Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Obecné

Hybrid Runbook Worker závisí na agentovi ke komunikaci s účtem Azure Automation k registraci pracovního procesu, příjmu úloh Runbooku a stavu sestavy. Pro Windows je tímto agentem agent Log Analytics pro Windows. Pro Linux se jedná o agenta Log Analytics pro Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scénář: spuštění sady Runbook se nezdařilo

#### <a name="issue"></a>Problém

Spuštění sady Runbook se nezdařilo a zobrazí se následující chybová zpráva:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Vaše sada Runbook je pozastavena krátce poté, co se pokusí provést tři časy. Existují podmínky, které mohou přerušit dokončení sady Runbook. Související chybová zpráva nemusí obsahovat žádné další informace.

#### <a name="cause"></a>Příčina

Níže jsou uvedené možné příčiny:

* Runbooky nelze ověřit pomocí místních prostředků.
* Hybrid Worker je za proxy serverem nebo bránou firewall.
* Počítač nakonfigurovaný na spouštění Hybrid Runbook Worker nesplňuje minimální požadavky na hardware.

#### <a name="resolution"></a>Řešení

Ověřte, zda má počítač odchozí přístup k **\* . Azure-Automation.NET** na portu 443.

Počítače, na kterých běží Hybrid Runbook Worker, by měly splňovat minimální požadavky na hardware před tím, než se pracovní proces nakonfiguruje na hostování této funkce. Runbooky a procesy na pozadí, které používají, by mohly způsobit, že se systém přeužije a dojde ke zpoždění nebo vypršení platnosti úlohy Runbooku.

Ověřte, že počítač, který spouští funkci Hybrid Runbook Worker, splňuje minimální požadavky na hardware. V takovém případě monitoruje využití procesoru a paměti k určení korelace mezi výkonem Hybrid Runbook Workerch procesů a oken. Nároky na paměť nebo procesor můžou znamenat nutnost upgradu prostředků. Můžete také vybrat jiný výpočetní prostředek, který podporuje minimální požadavky a škálování v případě požadavků na zatížení, což znamená, že je nutné zvýšit kapacitu.

V protokolu událostí **Microsoft-SMA** vyhledejte odpovídající událost s popisem `Win32 Process Exited with code [4294967295]` . Příčinou této chyby je, že v sadách Runbook jste nenakonfigurovali ověřování nebo jste určili přihlašovací údaje spustit jako pro skupinu Hybrid Runbook Worker. Zkontrolujte oprávnění sady Runbook ve [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) a ověřte, že jste správně nakonfigurovali ověřování pro vaše Runbooky.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scénář: událost 15011 v Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Hybrid Runbook Worker přijímá událost 15011, která značí, že výsledek dotazu není platný. Následující chyba se zobrazí, když se pracovní proces pokusí otevřít připojení k [serveru signalizace](/aspnet/core/signalr/introduction).

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Příčina

Hybrid Runbook Worker není správně nakonfigurovaný pro nasazení automatizované funkce, například pro Update Management. Nasazení obsahuje součást, která připojuje virtuální počítač k pracovnímu prostoru Log Analytics. Skript PowerShellu vyhledá pracovní prostor v předplatném se zadaným názvem. V tomto případě je Log Analytics pracovní prostor v jiném předplatném. Skript nemůže najít pracovní prostor a pokusí se ho vytvořit, ale název už je obsazený. V důsledku toho nasazení neproběhne úspěšně.

#### <a name="resolution"></a>Řešení

Tento problém můžete vyřešit dvěma způsoby:

* Upravte skript prostředí PowerShell tak, aby hledal pracovní prostor Log Analytics v jiném předplatném. Toto je dobré řešení, které můžete použít, pokud plánujete nasadit mnoho Hybrid Runbook Worker počítačů v budoucnu.

* Ručně nakonfigurujte pracovní počítač tak, aby běžel v izolovaném prostoru produktu Orchestrator. Pak spusťte sadu Runbook vytvořenou v účtu Azure Automation pracovního procesu a otestujte její funkčnost.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scénář: virtuální počítače Windows Azure automaticky vyřazené ze skupiny hybridních pracovních procesů

#### <a name="issue"></a>Problém

Hybrid Runbook Worker ani virtuální počítače se nezobrazí, pokud byl pracovní počítač vypnutý po dlouhou dobu.

#### <a name="cause"></a>Příčina

Hybrid Runbook Worker počítač neodpověděl Azure Automation po dobu delší než 30 dní. V důsledku toho automatizace vymazala skupinu Hybrid Runbook Worker nebo skupinu systémových pracovních procesů. 

#### <a name="resolution"></a>Řešení

Spusťte pracovní počítač a rereregister ho pomocí Azure Automation. Pokyny k instalaci prostředí Runbooku a připojení k Azure Automation najdete v tématu [nasazení Hybrid Runbook Worker Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scénář: v úložišti certifikátů na Hybrid Runbook Worker nebyl nalezen žádný certifikát.

#### <a name="issue"></a>Problém

Sada Runbook spuštěná na Hybrid Runbook Worker se nezdařila s následující chybovou zprávou:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud se pokusíte použít [účet Spustit jako](../automation-security-overview.md#run-as-accounts) v sadě Runbook, která běží na Hybrid Runbook Worker, kde certifikát účtu Spustit jako není k dispozici. Procesy Hybrid Runbook Worker nemají ve výchozím nastavení prostředek certifikátu. Účet Spustit jako vyžaduje, aby tento Asset správně fungoval.

#### <a name="resolution"></a>Řešení

Pokud je vaše Hybrid Runbook Worker VIRTUÁLNÍm počítačem Azure, můžete místo toho použít [ověřování Runbooku se spravovanými identitami](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) . Tento scénář zjednodušuje ověřování tím, že umožňuje ověřování prostředků Azure pomocí spravované identity virtuálního počítače Azure namísto účtu Spustit jako. Když Hybrid Runbook Worker je místní počítač, musíte na tento počítač nainstalovat certifikát účtu Spustit jako. Informace o tom, jak nainstalovat certifikát, najdete v tématu spuštění PowerShellu **Export-RunAsCertificateToHybridWorker** v PowerShellu pro [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scénář: Chyba 403 během registrace Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Fáze prvotní registrace pracovního procesu se nezdařila a zobrazí se následující chyba (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Příčina

Následující problémy jsou možné příčiny:

* V nastavení agenta je ID pracovního prostoru nebo klíč pracovního prostoru (primární). 
* Hybrid Runbook Worker nemůže stáhnout konfiguraci, což způsobí chybu při propojování účtu. Když Azure povolí funkce na počítačích, podporuje jenom některé oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Je také možné, že je v počítači nastaveno nesprávné datum nebo čas. Pokud je čas +/-15 minut od aktuálního času, nasazení funkcí se nepovede.

#### <a name="resolution"></a>Řešení

##### <a name="mistyped-workspace-id-or-key"></a>ID nebo klíč netypového pracovního prostoru
Pokud chcete ověřit, jestli se ID pracovního prostoru nebo klíč pracovního prostoru agenta nevytvořilo, přečtěte si téma [Přidání nebo odebrání pracovního prostoru – agent](../../azure-monitor/agents/agent-manage.md#windows-agent) pro Windows pro agenta Windows nebo [Přidání nebo odebrání pracovního prostoru – agenta Linux](../../azure-monitor/agents/agent-manage.md#linux-agent) pro agenta Linux. Ujistěte se, že jste vybrali úplný řetězec z Azure Portal a pečlivě ho zkopírujte a vložte.

##### <a name="configuration-not-downloaded"></a>Konfigurace nebyla stažena.

Váš pracovní prostor Log Analytics a účet Automation musí být v propojené oblasti. Seznam podporovaných oblastí naleznete v tématu [Azure Automation a Log Analytics mapování pracovních prostorů](../how-to/region-mappings.md).

Může být také nutné aktualizovat datum nebo časové pásmo počítače. Pokud vyberete vlastní časový rozsah, ujistěte se, že je rozsah v UTC, což se může lišit od místního časového pásma.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Scénář: Set-AzStorageBlobContent se nezdařila Hybrid Runbook Worker 

#### <a name="issue"></a>Problém

Při pokusu o spuštění sady Runbook dojde k `Set-AzStorageBlobContent` chybě a zobrazí se následující chybová zpráva:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Příčina

 Tato chyba je způsobená chováním dlouhého názvu souboru volání, `[System.IO.Path]::GetFullPath()` která přidávají cesty UNC.

#### <a name="resolution"></a>Řešení

Jako alternativní řešení můžete vytvořit konfigurační soubor `OrchestratorSandbox.exe.config` s názvem s následujícím obsahem:

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

Uložte tento soubor do stejné složky, ve které je spustitelný soubor `OrchestratorSandbox.exe` . Třeba

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Pokud upgradujete agenta, tento konfigurační soubor se odstraní a bude nutné ho znovu vytvořit.

## <a name="linux"></a>Linux

Hybrid Runbook Worker pro Linux závisí na [agentovi Log Analytics pro Linux](../../azure-monitor/agents/log-analytics-agent.md) ke komunikaci s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh Runbooku a stavu sestavy. V případě, že se registrace pracovního procesu nezdařila, můžete k chybě použít některé možné příčiny.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scénář: Linux Hybrid Runbook Worker přijímá při podepisování Runbooku výzvu k zadání hesla

#### <a name="issue"></a>Problém

Spuštění `sudo` příkazu pro Linux Hybrid Runbook Worker načte neočekávanou výzvu k zadání hesla.

#### <a name="cause"></a>Příčina

Účet **nxautomationuser** pro agenta Log Analytics pro Linux není správně nakonfigurovaný v souboru **sudoers** . Hybrid Runbook Worker potřebuje odpovídající konfiguraci oprávnění účtu a dalších dat, aby mohla podepisovat Runbooky na Linux Runbook Worker.

#### <a name="resolution"></a>Řešení

* Ujistěte se, že Hybrid Runbook Worker má na počítači spustitelný soubor GnuPG (GPG).

* Ověřte konfiguraci účtu **nxautomationuser** v souboru **sudoers** . Viz [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scénář: Agent Log Analytics pro Linux neběží.

#### <a name="issue"></a>Problém

Agent Log Analytics pro Linux neběží.

#### <a name="cause"></a>Příčina

Pokud agent neběží, zabrání Hybrid Runbook Worker pro Linux komunikovat s Azure Automation. Je možné, že agent neběží z různých důvodů.

#### <a name="resolution"></a>Řešení

 Ověřte, že je agent spuštěný, zadáním příkazu `ps -ef | grep python` . Výstup by se měl podobat následujícímu. Python zpracuje s uživatelským účtem **nxautomation** . Pokud funkce Azure Automation není povolená, žádný z následujících procesů není spuštěný.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

V následujícím seznamu jsou uvedeny procesy, které jsou spuštěny pro Hybrid Runbook Worker pro Linux. Všechno jsou umístěné v adresáři/var/opt/Microsoft/omsagent/State/automationworker/.

* **OMS. conf**: proces správce pracovních procesů. Spouští se přímo z DSC.
* **Work. conf**: automaticky registrovaný hybridní pracovní proces. Spouští ho správce pracovních procesů. Tento proces používá Update Management a je pro uživatele transparentní. Tento proces není k dispozici, pokud na počítači není povolená Update Management.
* **svépomocná/Worker. conf**: hybridní pracovní proces svépomocná. Hybridní pracovní proces SVÉPOMOCNÁ slouží ke spouštění sad Runbook na Hybrid Runbook Worker. Liší se pouze od automaticky registrovaného hybridního pracovního procesu v podrobnostech klíče, který používá jinou konfiguraci. Tento proces není k dispozici, pokud je Azure Automation zakázaná a Hybrid Worker SVÉPOMOCNÁ Linux není zaregistrovaný.

Pokud agent neběží, spusťte následující příkaz, který službu spustí: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scénář: Zadaná třída neexistuje.

Pokud se v/var/opt/Microsoft/omsconfig/omsconfig.log zobrazí chybová `The specified class does not exist..` zpráva , je nutné aktualizovat agenta Log Analytics pro Linux. Spusťte následující příkaz pro přeinstalaci agenta.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrid Runbook Worker Windows závisí na [agentu Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) , aby Windows komunikoval s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh Runbooku a stavu sestavy. V případě, že se registrace pracovního procesu nezdařila, obsahuje tato část některé z možných důvodů.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scénář: Agent Log Analytics pro Windows není spuštěný.

#### <a name="issue"></a>Problém

Na `healthservice` Hybrid Runbook Workerovém počítači není spuštěná.

#### <a name="cause"></a>Příčina

Pokud Log Analytics pro službu Windows není spuštěná, Hybrid Runbook Worker nemůže komunikovat s Azure Automation.

#### <a name="resolution"></a>Řešení

Zadáním následujícího příkazu v PowerShellu ověřte, že je agent spuštěný: `Get-Service healthservice` . Pokud je služba zastavená, zadejte do PowerShellu následující příkaz, kterým službu spustíte: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scénář: Událost 4502 v protokolu Operations Manager

#### <a name="issue"></a>Problém

V protokolu událostí **Application and Services Logs\Operations Manager** uvidíte událost 4502 a zprávu události, která obsahuje `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` následující popis:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Příčina

Důvodem může být to, že proxy server nebo brána firewall sítě blokuje komunikaci s Microsoft Azure. Ověřte, zda má počítač odchozí přístup k **\* . Azure-Automation.NET** na portu 443.

#### <a name="resolution"></a>Řešení

Protokoly se ukládají místně na každý hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.. V protokolech událostí **aplikace a služby Logs\Microsoft-SMA\Operations** a **Application and Services Logs\Operations Manager** můžete ověřit, zda jsou k dispozici nějaké události upozornění nebo chyby. Tyto protokoly označují připojení nebo jiný typ problému, který má vliv na povolení Azure Automation role, nebo při běžných operacích došlo k potížím. Další pomoc při řešení potíží s agentem Log Analytics najdete v tématu [řešení potíží s agentem Log Analytics Windows](../../azure-monitor/agents/agent-windows-troubleshoot.md).

Hybridní pracovní procesy odesílají [výstup a zprávy Runbooku](../automation-runbook-output-and-messages.md) do Azure Automation stejným způsobem jako úlohy Runbooku běžící v cloudu odesílají výstup a zprávy. Můžete povolit streamování s podrobnými a průběhy stejně jako u runbooků.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scénář: Orchestrator.Sandbox.exe se nemůže připojit k Microsoft 365 prostřednictvím proxy

#### <a name="issue"></a>Problém

Skript spuštěný ve Windows Hybrid Runbook Worker se nemůže připojit podle očekávání, aby Microsoft 365 v izolovaném prostoru (sandbox) Orchestrator. Skript používá pro připojení [příkaz Connect-MsolService](/powershell/module/msonline/connect-msolservice) . 

Pokud upravíte **Orchestrator.Sandbox.exe.config** nastavíte proxy server a seznam pro obejití, izolovaný prostor (sandbox) se stále nepřipojí správně. **Powershell_ise.exe.config** soubor se stejným nastavením proxy serveru a seznamu obcházení se zdá fungovat podle očekávání. Protokoly Service Management Automation (SMA) a protokoly prostředí PowerShell neposkytují žádné informace týkající se proxy serveru.

#### <a name="cause"></a>Příčina

Připojení k Active Directory Federation Services (AD FS) (AD FS) na serveru nemůže obejít proxy server. Pamatujte, že izolovaný prostor (sandbox) PowerShellu běží jako přihlášený uživatel. Izolovaný prostor Orchestrator je ale silně přizpůsobený a může **Orchestrator.Sandbox.exe.config** nastavení souboru ignorovat. Má speciální kód pro zpracování nastavení proxy serveru nebo Log Analytics agenta, ale ne pro zpracování jiných vlastních nastavení proxy serveru. 

#### <a name="resolution"></a>Řešení

Problém pro izolovaný prostor pro Orchestrator můžete vyřešit migrací skriptu tak, aby používal moduly Azure Active Directory místo modulu MSOnline pro rutiny prostředí PowerShell. Další informace najdete v tématu [migrace z nástroje Orchestrator na Azure Automation (beta)](../automation-orchestrator-migration.md).

Pokud chcete i nadále používat rutiny modulu MSOnline, změňte skript tak, aby používal [příkaz Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command). Zadejte hodnoty `ComputerName` `Credential` parametrů a. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Tato změna kódu spustí zcela novou relaci PowerShellu v kontextu zadaných přihlašovacích údajů. Měl by Povolit tok dat proxy server, který ověřuje aktivního uživatele.

>[!NOTE]
>Toto řešení znemožňuje nepotřebnou manipulaci s konfiguračním souborem izolovaného prostoru (sandbox). I v případě, že jste úspěšně napracovali s vaším skriptem v konfiguračním souboru, soubor se vymaže při každém aktualizování agenta Hybrid Runbook Worker.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scénář: Hybrid Runbook Worker nevytváření sestav

#### <a name="issue"></a>Problém

Počítač s Hybrid Runbook Worker je spuštěný, ale nevidíte data prezenčního signálu pro daný počítač v pracovním prostoru.

Následující příklad dotazu zobrazuje počítače v pracovním prostoru a jejich poslední prezenční signál:

```kusto
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

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Scénář: nemůžete přidat Hybrid Runbook Worker Windows.

#### <a name="issue"></a>Problém

Při pokusu o přidání Hybrid Runbook Worker pomocí rutiny se zobrazí následující zpráva `Add-HybridRunbookWorker` :

`Machine is already registered`

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je počítač již zaregistrován s jiným účtem služby Automation nebo pokud se pokusíte znovu přidat Hybrid Runbook Worker po jeho odebrání z počítače.

#### <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, odeberte následující klíč registru, restartujte počítač `HealthService` a zkuste `Add-HybridRunbookWorker` rutinu znovu.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Scénář: nemůžete přidat Hybrid Runbook Worker pro Linux.

#### <a name="issue"></a>Problém

Při pokusu o přidání Hybrid Runbook Worker pomocí skriptu Pythonu se zobrazí následující zpráva `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` :

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Kromě toho se při pokusu o zrušení registrace Hybrid Runbook Worker pomocí `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` skriptu Pythonu:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je počítač už zaregistrovaný v jiném účtu Automation, pokud se odstranila skupina Azure Hybrid Worker, nebo pokud se pokusíte Hybrid Runbook Worker znovu přidat po jeho odebrání z počítače.

#### <a name="resolution"></a>Řešení

Řešení tohoto problému:

1. Odeberte agenta `sudo sh onboard_agent.sh --purge` .

1. Spusťte tyto příkazy:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Opětovné zprovoznění agenta `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Počkejte, než se složka `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` naplní.

1. Zkuste `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` znovu skript Pythonu.

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.
