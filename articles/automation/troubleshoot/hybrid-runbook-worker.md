---
title: Řešení potíží – služby Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o řešení potíží s Azure Automation Hybrid Runbook Worker
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a95c9f1edd6983c915316f2900885a8131245860
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437822"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Řešení potíží s procesy Hybrid Runbook Worker

Tento článek obsahuje informace o odstraňování potíží s procesy Hybrid Runbook Worker.

## <a name="general"></a>Obecné

Funkce Hybrid Runbook Worker, závisí na agenta ke komunikaci s účtem služby Automation k registraci pracovního procesu, přijímat úlohy runbooku a informuje o stavu. Pro Windows je tento agent Microsoft Monitoring Agent. Pro Linux je agenta OMS pro Linux.

### <a name="runbook-execution-fails"></a>Scénář: Spuštění sady Runbook selže

#### <a name="issue"></a>Problém

Spuštění sady Runbook selže a zobrazí se následující chyba:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Vaše sada runbook je pozastavený, krátce po pokusu o provedení jeho třikrát. Existují podmínky, které mohou narušit runbook úspěšně dokončit a související chybové zprávy neobsahuje další informace, který označuje důvod, proč.

#### <a name="cause"></a>Příčina

Toto jsou možné příčiny:

* Runbook nejde ověřit s místními prostředky

* Hybridní pracovní proces je za proxy nebo brány firewall

* Runbook nejde ověřit s místními prostředky

* Počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální hardwarové požadavky.

#### <a name="resolution"></a>Řešení

Ověřte, že má počítač odchozí přístup k *.azure-automation.net na portu 443.

Počítače se systémem Hybrid Runbook Worker by měl splňovat minimální požadavky na hardware před určit ji hostovat tuto funkci. V závislosti na využití prostředků z jiné procesy na pozadí a během může spuštění sady runbook v opačném případě příčina počítač stane přetížen a způsobit zpoždění úlohy sady runbook nebo vypršení časových limitů.

Potvrďte, že počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální hardwarové požadavky. Pokud ano, monitorování využití procesoru a paměti určit jakákoli korelace mezi výkonem procesy Hybrid Runbook Worker a Windows. Pokud je paměť nebo procesor přetížení, může to znamenat nemusíte upgradovat nebo přidání dalších procesorů nebo navyšte paměť adres kritickým bodem prostředků a vyřešení chyby. Alternativně vyberte různé výpočetního prostředku, který podporuje požadavky na minimální a škálování při vytížení označující, že zvýšení je nezbytné.

Zkontrolujte **Microsoft SMA** protokolu událostí pro odpovídající události s popisem *Win32 proces skončil s kódem [4294967295]*. Příčinou této chyby je nebyly nakonfigurované ověřování ve vašich sadách runbook nebo zadaná pověření spustit jako pro skupinu hybridních pracovních procesů. Kontrola [oprávnění sady Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) potvrďte jste správně nakonfigurovali ověřování pro vlastní runbooky.

## <a name="linux"></a>Linux

Hybrid Runbook Worker Linuxu závisí na agenta OMS pro Linux ke komunikaci s účtem služby Automation k registraci pracovního procesu, přijímat úlohy runbooku a informuje o stavu. Pokud pracovní proces registrace selže, tady jsou některé možné příčiny chyby:

### <a name="oms-agent-not-running"></a>Scénář: Není spuštěn Agent OMS pro Linux

Pokud není spuštěn Agent OMS pro Linux, zabrání Hybrid Runbook Worker Linuxu komunikaci s Azure Automation. Ověření, je agent spuštěn tak, že zadáte následující příkaz: `ps -ef | grep python`. Byste měli vidět výstup podobný následujícímu procesů python pomocí **nxautomation** uživatelský účet. Pokud nejsou povolené řešení Update Management nebo služby Azure Automation, žádný z následujících postupů běží.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Následující seznam uvádí procesy, které jsou spouštěny pro Linux Hybrid Runbook Worker. Všechny nacházejí v `/var/opt/microsoft/omsagent/state/automationworker/` adresáře.

* **OMS.conf** – tento proces je správce pracovního procesu, tento proces je spuštěn přímo z DSC.

* **Worker.conf** -tohoto procesu je automaticky zaregistrovaný hybridní pracovní proces, se spustí Správce pracovních procesů. Tento proces používá správu aktualizací a je pro uživatele transparentní. Tento proces není k dispozici, pokud na počítači není povolené řešení Update Management.

* **diy/Worker.conf** – tento proces je rozběhněte zcela hybridní pracovní proces. Rozběhněte zcela hybridní pracovní proces se používá k provedení uživatele runbooky v procesu Hybrid Runbook Worker. Se liší pouze od automatického zaregistrovaný hybridní pracovní proces v klíčových podrobností, které se používá jinou konfiguraci. Tento proces není k dispozici, pokud řešení Azure Automation není povoleno a není registrovaný rozběhněte zcela Hybrid Worker v Linuxu.

Pokud agenta OMS pro Linux neběží, spusťte následující příkaz ke spuštění služby: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scénář: Zadanou třídu neexistuje.

Pokud se zobrazí následující chyba: **Zadaná třída neexistuje...** v `/var/opt/microsoft/omsconfig/omsconfig.log` pak agenta OMS pro Linux je potřeba aktualizovat. Spusťte následující příkaz, který znovu nainstalujte agenta OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Funkce Hybrid Runbook Worker Windows závisí na agenta Microsoft Monitoring Agent ke komunikaci s účtem služby Automation k registraci pracovního procesu, přijímat úlohy runbooku a informuje o stavu. Pokud pracovní proces registrace selže, tady jsou některé možné příčiny chyby:

### <a name="mma-not-running"></a>Scénář: Microsoft Monitoring Agent není spuštěna.

#### <a name="issue"></a>Problém

`healthservice` Služba není spuštěná na počítači Hybrid Runbook Worker.

#### <a name="cause"></a>Příčina

Pokud služba Microsoft Monitoring agenta Windows není spuštěna, tento scénář brání Hybrid Runbook Worker v komunikaci s Azure Automation.

#### <a name="resolution"></a>Řešení

Ověření, je agent spuštěn tak, že zadáte následující příkaz Powershellu: `Get-Service healthservice`. Pokud je služba zastavená, zadáním následujícího příkazu v prostředí PowerShell spustit službu: `Start-Service healthservice`.

### <a name="event-4502"></a> Události 4502 v protokolu Operations Manager

#### <a name="issue"></a>Problém

V **Application and Services Logs\Operations Manager** protokolu událostí, zobrazí se události 4502 a zpráva události, která obsahuje **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**s tímto popisem: *Certifikát předložený službou \<wsid\>. oms.opinsights.azure.com nevydala certifikační autorita používaná pro služby Microsoftu. Obraťte se prosím na správce sítě a zjistěte, jestli jsou spuštěné proxy server, který zachycuje komunikace TLS/SSL. Najdete v článku KB3126513 obsahuje další informace o připojení k řešení potíží.*

#### <a name="cause"></a>Příčina

Tento problém může být způsobeno proxy serveru nebo síťové brány firewall blokující komunikaci s Microsoft Azure. Ověřte, že má počítač odchozí přístup k *.azure-automation.net porty 443.

#### <a name="resolution"></a>Řešení

Protokoly se ukládají místně na každé hybridní pracovní proces v C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Můžete zkontrolovat, zda je žádné upozornění nebo chybové události v **aplikace a služby Logs\Microsoft-SMA\Operations** a **Application and Services Logs\Operations Manager** protokolu událostí, který by označení připojení nebo jiný problém, který má vliv na připojení role do Azure Automation nebo problém v rámci normálního provozu.

[Sada Runbook výstup a zprávy](../automation-runbook-output-and-messages.md) jsou odesílány do Azure Automation hybrid Worker stejně jako úlohy runbooku, který běží v cloudu. Můžete také povolit podrobné a průběhu zpracování datových proudů stejně jako byste to udělali pro jiné sady runbook.

### <a name="corrupt-cache"></a> Funkce hybrid Runbook Worker nevytvářejících sestavy

#### <a name="issue"></a>Problém

Funkce Hybrid Runbook Worker počítač běží, ale nevidíte dat prezenčního signálu pro počítač v pracovním prostoru.

Následující příklad dotazu zobrazí v pracovním prostoru a jejich poslední prezenční signál počítače:

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Příčina

Tento problém může být způsobeno poškozený mezipaměti v procesu Hybrid Runbook Worker.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, přihlaste se k procesu Hybrid Runbook Worker a spusťte následující skript. Tento skript agenta Microsoft Monitoring Agent zastaví, odebere uloženou v mezipaměti a restartuje službu. Tato akce vynutí Hybrid Runbook Worker znovu stáhnout jeho konfiguraci ze služby Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
