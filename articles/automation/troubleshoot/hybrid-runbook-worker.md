---
title: Řešení potíží – Azure Automation procesů Hybrid Runbook Worker
description: Tento článek poskytuje informace, které Azure Automation procesy Hybrid Runbook Worker k řešení potíží.
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39cf6126f6212b6e83f1974dae7aaab0038e69c6
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240983"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Řešení potíží s procesy Hybrid Runbook Worker

Tento článek poskytuje informace o řešení potíží s procesy Hybrid Runbook Worker.

## <a name="general"></a>Obecné

Hybrid Runbook Worker závisí na agentovi ke komunikaci s vaším účtem Automation za účelem registrace pracovního procesu, příjmu úloh sady Runbook a stavu sestavy. Pro Windows se jedná o Microsoft Monitoring Agent tohoto agenta. Pro Linux se jedná o Agent sady OMS pro Linux.

### <a name="runbook-execution-fails"></a>Případě Spuštění sady Runbook se nezdařilo

#### <a name="issue"></a>Problém

Spuštění sady Runbook se nezdařilo a zobrazí se následující chyba:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Vaše sada Runbook se krátce pozastaví, jakmile se pokusí ji spustit třikrát. Existují podmínky, které mohou přerušit dokončení sady Runbook. Pokud k tomu dojde, související chybová zpráva nemusí obsahovat žádné další informace, které vám pomohou.

#### <a name="cause"></a>Příčina

Níže jsou uvedené možné příčiny:

* Runbooky se nedají ověřit u místních prostředků.

* Hybridní pracovní proces je za proxy serverem nebo bránou firewall.

* Runbooky se nedají ověřit u místních prostředků.

* Počítač nakonfigurovaný pro spuštění funkce Hybrid Runbook Worker nesplňuje minimální požadavky na hardware.

#### <a name="resolution"></a>Řešení

Ověřte, zda má počítač odchozí přístup k *. azure-automation.net na portu 443.

Počítače, na kterých běží Hybrid Runbook Worker, by měly splňovat minimální požadavky na hardware před tím, než se nakonfigurují pro hostování této funkce. Runbooky a procesy na pozadí, které používají, můžou způsobit, že se systém využije a dojde ke zpoždění nebo vypršení platnosti úlohy Runbooku.

Ověřte, že počítač, který spustí funkci Hybrid Runbook Worker, splňuje minimální požadavky na hardware. V takovém případě monitoruje využití procesoru a paměti k určení korelace mezi výkonem Hybrid Runbook Workerch procesů a oken. Pokud dojde k paměti nebo přetížení procesoru, může to znamenat nutnost upgradu prostředků. Můžete také vybrat jiný výpočetní prostředek, který může podporovat minimální požadavky a škálování v případě požadavků na zatížení, což znamená, že je potřeba zvýšit kapacitu.

V protokolu událostí **Microsoft-SMA** vyhledejte odpovídající událost s popisem *procesu Win32, který skončil s kódem [4294967295]* . Příčinou této chyby je, že v sadách Runbook jste nenakonfigurovali ověřování nebo jste zadali přihlašovací údaje spustit jako pro skupinu hybridních pracovních procesů. Zkontrolujte [oprávnění sady Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) a potvrďte, že jste správně nakonfigurovali ověřování pro vaše Runbooky.

### <a name="no-cert-found"></a>Případě V úložišti certifikátů v Hybrid Runbook Worker nebyl nalezen žádný certifikát.

#### <a name="issue"></a>Problém

Sada Runbook spuštěná na Hybrid Runbook Worker se nezdařila s následující chybovou zprávou:

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

Pokud je vaším Hybrid Runbook Worker VIRTUÁLNÍm počítačem Azure, můžete místo toho použít [spravované identity pro prostředky Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Tento scénář umožňuje ověření pro prostředky Azure pomocí spravované identity virtuálního počítače Azure místo účtu Spustit jako, což zjednodušuje ověřování. Když Hybrid Runbook Worker je místní počítač, musíte na tento počítač nainstalovat certifikát účtu Spustit jako. Informace o tom, jak nainstalovat certifikát, najdete v tématu spuštění sady Runbook [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) .

## <a name="linux"></a>Linux

Hybrid Runbook Worker pro Linux závisí na Agent sady OMS pro Linux ke komunikaci s vaším účtem Automation k registraci pracovního procesu, příjmu úloh Runbooku a stavu sestavy. Pokud se registrace pracovního procesu nezdařila, můžete k této chybě použít některé možné příčiny:

### <a name="oms-agent-not-running"></a>Případě Agent sady OMS pro Linux neběží.

#### <a name="issue"></a>Problém

Agent sady OMS pro Linux neběží.

#### <a name="cause"></a>Příčina

Pokud Agent sady OMS pro Linux neběží, brání Hybrid Runbook Worker pro Linux komunikaci s Azure Automation. Agent možná není spuštěný z různých důvodů.

#### <a name="resolution"></a>Řešení

 Ověřte, jestli je agent spuštěný, a to zadáním následujícího příkazu: `ps -ef | grep python`. Měl by se zobrazit výstup podobný následujícímu: aplikace Python zpracuje s uživatelským účtem **nxautomation** . Pokud nejsou povolena řešení Update Management nebo Azure Automation, není spuštěn žádný z následujících procesů.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

V následujícím seznamu jsou uvedeny procesy, které jsou spuštěny pro Hybrid Runbook Worker pro Linux. Všechno jsou umístěné v `/var/opt/microsoft/omsagent/state/automationworker/` adresáři.


* **OMS. conf** – tato hodnota je proces správce pracovních procesů. Spouští se přímo z DSC.

* **Worker. conf** – tento proces je automaticky registrovaný Hybrid Worker, který je spuštěný správcem pracovních procesů. Tento proces používá Update Management a je pro uživatele transparentní. Tento proces není k dispozici, pokud řešení Update Management není na počítači povoleno.

* **svépomocná/Worker. conf** – tento proces je hybridní pracovní proces svépomocná. Hybridní pracovní proces SVÉPOMOCNÁ slouží ke spouštění sad Runbook na Hybrid Runbook Worker. Liší se pouze od automaticky registrovaného hybridního pracovního procesu v podrobnostech klíče, který používá jinou konfiguraci. Tento proces není k dispozici, pokud je řešení Azure Automation zakázané a Hybrid Worker SVÉPOMOCNÁ Linux není zaregistrované.

Pokud Agent sady OMS pro Linux neběží, spusťte následující příkaz, který službu spustí: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Případě Zadaná třída neexistuje.

Pokud se zobrazí následující chyba: **Zadaná třída neexistuje...** `/var/opt/microsoft/omsconfig/omsconfig.log` v agent sady OMS pro Linux je třeba aktualizovat. Spusťte následující příkaz, který znovu nainstaluje agenta OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Hybrid Runbook Worker Windows závisí na Microsoft Monitoring Agent ke komunikaci s vaším účtem Automation k registraci pracovního procesu, příjmu úloh Runbooku a stavu sestavy. Pokud se registrace pracovního procesu nezdařila, můžete k této chybě použít některé možné příčiny:

### <a name="mma-not-running"></a>Případě Microsoft Monitoring Agent neběží.

#### <a name="issue"></a>Problém

`healthservice` Služba není na Hybrid Runbook Workerm počítači spuštěna.

#### <a name="cause"></a>Příčina

Pokud služba Microsoft Monitoring Agent Windows není spuštěná, brání tento stav Hybrid Runbook Worker komunikaci s Azure Automation.

#### <a name="resolution"></a>Řešení

Ověřte, že je agent spuštěný, a to zadáním následujícího `Get-Service healthservice`příkazu v PowerShellu:. Pokud je služba zastavená, zadejte do PowerShellu následující příkaz, kterým službu spustíte: `Start-Service healthservice`.

### <a name="event-4502"></a>Událost 4502 v protokolu Operations Manager

#### <a name="issue"></a>Problém

V protokolu událostí **Application and Services Logs\Operations Manager** uvidíte událost 4502 a EventMessage obsahující **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** s následujícím popisem: *Certifikát předložený službou \<WSID\>. OMS.opinsights.Azure.com nebyl vydán certifikační autoritou používanou pro služby společnosti Microsoft. Obraťte se na správce sítě a zjistěte, jestli se používá proxy server, který zachycuje komunikaci TLS/SSL. V článku KB3126513 najdete další informace pro řešení problémů s připojením.*

#### <a name="cause"></a>Příčina

Důvodem může být to, že proxy server nebo brána firewall sítě blokuje komunikaci s Microsoft Azure. Ověřte, že počítač má odchozí přístup k *. azure-automation.net na portech 443.

#### <a name="resolution"></a>Řešení

Protokoly se ukládají místně na každý hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Můžete zjistit, zda se v protokolu událostí **Logs\Microsoft-SMA\Operations** a Application and Services a **Application and Services Logs\Operations Manager** vyskytnou nějaké události s varováním nebo chybou, které by označovaly připojení nebo jiný problém má vliv na připojení role k Azure Automation nebo k vydání během normálních operací.

[Výstup runbooku a zprávy](../automation-runbook-output-and-messages.md) se odesílají Azure Automation z hybridních pracovních procesů stejně jako úlohy Runbooku, které běží v cloudu. Můžete také povolit streamování verbose a průběh stejným způsobem jako jiné sady Runbook.

### <a name="corrupt-cache"></a>Nevytváření sestav Hybrid Runbook Worker

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

### <a name="already-registered"></a>Případě Nemůžete přidat Hybrid Runbook Worker

#### <a name="issue"></a>Problém

Při pokusu o přidání Hybrid Runbook Worker pomocí `Add-HybridRunbookWorker` rutiny se zobrazí následující zpráva.

```error
Machine is already registered
```

#### <a name="cause"></a>Příčina

To může být způsobeno tím, že je počítač již zaregistrován s jiným účtem služby Automation, nebo pokud se pokusíte Hybrid Runbook Worker znovu přidat po jeho odebrání z počítače.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, odeberte následující klíč registru a restartujte `HealthService` příkaz a `Add-HybridRunbookWorker` znovu spusťte rutinu:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

