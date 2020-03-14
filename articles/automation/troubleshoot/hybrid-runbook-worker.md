---
title: Řešení potíží – Azure Automation procesů Hybrid Runbook Worker
description: Tento článek poskytuje informace, které Azure Automation procesy Hybrid Runbook Worker k řešení potíží.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252685"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Řešení potíží s procesy Hybrid Runbook Worker

Tento článek poskytuje informace o řešení potíží s procesy Hybrid Runbook Worker.

## <a name="general"></a>Obecné

Hybrid Runbook Worker závisí na agentovi ke komunikaci s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh sady Runbook a stavu sestavy. Pro Windows je tento agent agentem Log Analytics pro Windows, označovaný taky jako Microsoft Monitoring Agent (MMA). Pro Linux se jedná o agenta Log Analytics pro Linux.

### <a name="runbook-execution-fails"></a>Scénář: spuštění sady Runbook se nezdařilo

#### <a name="issue"></a>Problém

Spuštění sady Runbook se nezdařilo a zobrazí se následující chyba.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Vaše sada Runbook je pozastavena krátce poté, co se pokusí provést tři časy. Existují podmínky, které mohou přerušit dokončení sady Runbook. Související chybová zpráva nemusí obsahovat žádné další informace.

#### <a name="cause"></a>Příčina

Níže jsou uvedeny možné příčiny:

* Runbooky nelze ověřit pomocí místních prostředků.

* Hybrid Worker je za proxy serverem nebo bránou firewall.

* Počítač nakonfigurovaný pro spuštění funkce Hybrid Runbook Worker nesplňuje minimální požadavky na hardware.

#### <a name="resolution"></a>Řešení

Ověřte, zda má počítač odchozí přístup k *. azure-automation.net na portu 443.

Počítače, na kterých běží Hybrid Runbook Worker, by měly splňovat minimální požadavky na hardware před tím, než se pracovní proces nakonfiguruje na hostování této funkce. Runbooky a procesy na pozadí, které používají, by mohly způsobit, že systém bude použit opakovaně a může dojít ke zpoždění nebo vypršení platnosti úlohy Runbooku.

Ověřte, že počítač, který spouští funkci Hybrid Runbook Worker, splňuje minimální požadavky na hardware. V takovém případě monitoruje využití procesoru a paměti k určení korelace mezi výkonem Hybrid Runbook Workerch procesů a oken. Nároky na paměť nebo procesor můžou znamenat nutnost upgradu prostředků. Můžete také vybrat jiný výpočetní prostředek, který podporuje minimální požadavky a škálování v případě požadavků na zatížení, což znamená, že je nutné zvýšit kapacitu.

V protokolu událostí **Microsoft-SMA** vyhledejte odpovídající událost s popisem *procesu Win32, který skončil s kódem [4294967295]* . Příčinou této chyby je, že v sadách Runbook jste nenakonfigurovali ověřování nebo jste určili přihlašovací údaje spustit jako pro skupinu Hybrid Runbook Worker. Zkontrolujte oprávnění sady Runbook ve [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) a ověřte, zda jste správně nakonfigurovali ověřování pro vaše Runbooky.

### <a name="no-cert-found"></a>Scénář: v úložišti certifikátů na Hybrid Runbook Worker nebyl nalezen žádný certifikát.

#### <a name="issue"></a>Problém

Sada Runbook spuštěná na Hybrid Runbook Worker se nezdařila s následující chybovou zprávou.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Příčina

K této chybě dochází, pokud se pokusíte použít [účet Spustit jako](../manage-runas-account.md) v sadě Runbook, která běží na Hybrid Runbook Worker, kde není přítomen certifikát účtu Spustit jako. Hybridní pracovní procesy Runbooku nemají ve výchozím nastavení prostředek certifikátu, který je vyžadován pro správné fungování účtu Spustit jako.

#### <a name="resolution"></a>Řešení

Pokud je vaším Hybrid Runbook Worker VIRTUÁLNÍm počítačem Azure, můžete místo toho použít [spravované identity pro prostředky Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Tento scénář zjednodušuje ověřování tím, že umožňuje ověřování prostředků Azure pomocí spravované identity virtuálního počítače Azure namísto účtu Spustit jako. Když Hybrid Runbook Worker je místní počítač, musíte na tento počítač nainstalovat certifikát účtu Spustit jako. Informace o tom, jak nainstalovat certifikát, najdete v tématu spuštění PowerShellu export-RunAsCertificateToHybridWorker ve [spouštění Runbooků na Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Scénář: Chyba 403 během registrace Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Fáze prvotní registrace pracovního procesu se nezdařila a zobrazí se následující chyba (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Příčina

Níže jsou uvedeny možné příčiny:
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

### <a name="oms-agent-not-running"></a>Scénář: Agent Log Analytics pro Linux není spuštěný.

#### <a name="issue"></a>Problém

Agent Log Analytics pro Linux není spuštěný.

#### <a name="cause"></a>Příčina

Pokud agent neběží, zabrání Hybrid Runbook Worker pro Linux komunikovat s Azure Automation. Je možné, že agent neběží z různých důvodů.

#### <a name="resolution"></a>Řešení

 Ověřte, jestli je agent spuštěný, a to zadáním následujícího příkazu: `ps -ef | grep python`. Měl by se zobrazit výstup podobný následujícímu: aplikace Python zpracuje s uživatelským účtem **nxautomation** . Pokud nejsou povolena řešení Update Management nebo Azure Automation, není spuštěn žádný z následujících procesů.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

V následujícím seznamu jsou uvedeny procesy, které jsou spuštěny pro Hybrid Runbook Worker pro Linux. Všechno jsou umístěné v adresáři `/var/opt/microsoft/omsagent/state/automationworker/`.

* **OMS. conf** – proces správce pracovních procesů. Spouští se přímo z DSC.

* **Worker. conf** – automaticky registrovaný hybridní pracovní proces. Spouští ho správce pracovních procesů. Tento proces používá Update Management a je pro uživatele transparentní. Tento proces není k dispozici, pokud řešení Update Management není na počítači povoleno.

* **svépomocná/Worker. conf** – svépomocná Hybrid Worker. Hybridní pracovní proces SVÉPOMOCNÁ slouží ke spouštění sad Runbook na Hybrid Runbook Worker. Liší se pouze od automatického zaregistrovaného hybridního pracovního procesu v podrobnostech klíče, který používá jinou konfiguraci. Tento proces není k dispozici, pokud je řešení Azure Automation zakázané a Hybrid Worker SVÉPOMOCNÁ Linux není zaregistrované.

Pokud agent neběží, spusťte následující příkaz, který službu spustí: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scénář: Zadaná třída neexistuje.

Pokud se zobrazí chyba **, zadaná Třída neexistuje.** v `/var/opt/microsoft/omsconfig/omsconfig.log`se musí aktualizovat agent Log Analytics pro Linux. Spusťte následující příkaz pro přeinstalaci agenta:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrid Runbook Worker Windows závisí na [agentu Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) , aby Windows komunikoval s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh Runbooku a stavu sestavy. Pokud se registrace pracovního procesu nezdařila, můžete k této chybě použít některé možné příčiny:

### <a name="mma-not-running"></a>Scénář: Microsoft Monitoring Agent neběží.

#### <a name="issue"></a>Problém

Služba `healthservice` na Hybrid Runbook Workerm počítači neběží.

#### <a name="cause"></a>Příčina

Pokud Microsoft Monitoring Agent služba společnosti Microsoft neběží, brání tento stav Hybrid Runbook Worker komunikaci s Azure Automation.

#### <a name="resolution"></a>Řešení

Ověřte, že je agent spuštěný, a to zadáním následujícího příkazu v PowerShellu: `Get-Service healthservice`. Pokud je služba zastavená, zadejte do PowerShellu následující příkaz pro spuštění služby: `Start-Service healthservice`.

### <a name="event-4502"></a>Scénář: Událost 4502 v protokolu Operations Manager

#### <a name="issue"></a>Problém

V protokolu událostí **Application and Services Logs\Operations Manager** uvidíte událost 4502 a EventMessage obsahující **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** s následujícím popisem: *certifikát prezentovaný \<WSID\>. OMS.opinsights.Azure.com nebyl vydán certifikační autoritou, která se používá pro služby společnosti Microsoft. Obraťte se na správce sítě a zjistěte, jestli se používá proxy server, který zachycuje komunikaci TLS/SSL.*

#### <a name="cause"></a>Příčina

Důvodem může být to, že proxy server nebo brána firewall sítě blokuje komunikaci s Microsoft Azure. Ověřte, že počítač má odchozí přístup k *. azure-automation.net na portech 443. 

#### <a name="resolution"></a>Řešení

Protokoly se ukládají místně na každý hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Můžete ověřit, zda se v protokolu událostí Logs\Microsoft-SMA\Operations a Application and Services pro aplikace a **služby Logs\Operations** ( **Application** and Services) vyskytnou nějaké události s varováním nebo chybou, které označují připojení nebo jiný problém, který má vliv na registraci role Azure Automation nebo vydání při běžném provozu. Další pomoc při řešení potíží s agentem Log Analytics najdete v tématu [řešení potíží s agentem Log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

[Výstup runbooku a zprávy](../automation-runbook-output-and-messages.md) se odesílají Azure Automation z hybridních pracovních procesů stejně jako úlohy Runbooku, které běží v cloudu. Můžete také povolit streamování verbose a průběh stejným způsobem jako jiné sady Runbook.

### <a name="corrupt-cache"></a>Scénář: Hybrid Runbook Worker nevytváření sestav

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

Pokud chcete tento problém vyřešit, přihlaste se k Hybrid Runbook Worker a spusťte následující skript. Tento skript zastaví Microsoft Monitoring Agent, odebere jeho mezipaměť a restartuje službu. Tato akce vynutí, aby se Hybrid Runbook Worker znovu stáhla konfigurace z Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scénář: nemůžete přidat Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Při pokusu o přidání Hybrid Runbook Worker pomocí rutiny `Add-HybridRunbookWorker` se zobrazí následující zpráva.

```error
Machine is already registered
```

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je počítač již zaregistrován s jiným účtem služby Automation nebo pokud se pokusíte přečíst Hybrid Runbook Worker po jeho odebrání z počítače.

#### <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, odeberte následující klíč registru a restartujte `HealthService` a zkuste rutinu `Add-HybridRunbookWorker` znovu spustit:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.