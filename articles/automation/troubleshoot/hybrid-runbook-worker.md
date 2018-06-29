---
title: Řešení potíží – procesy služby Azure Automation Hybrid Runbook Worker
description: Tento článek obsahuje informace o řešení potíží s procesy Azure Automation Hybrid Runbook Worker
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063783"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Řešení potíží s procesy Hybrid Runbook Worker

Tento článek obsahuje informace o odstraňování potíží s procesy Hybrid Runbook Worker.

## <a name="general"></a>Obecné

Hybridní pracovní proces Runbooku závisí na agenta ke komunikaci s vaším účtem Automation k registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pro systém Windows je tohoto agenta Microsoft Monitoring Agent. Pro Linux je Agent OMS pro Linux.

###<a name="runbook-execution-fails"></a>Scénář: Spuštění sady Runbook nezdaří

#### <a name="issue"></a>Problém

Spuštění sady Runbook se nezdaří a zobrazí se následující chyba:

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Vaše sada runbook je pozastaven krátce po pokusu o spuštění ho třikrát. Existují podmínky, které mohou přerušit sady runbook nelze úspěšně dokončit a související chybová zpráva neobsahuje žádné další údaje, která udává, proč.

#### <a name="cause"></a>Příčina

Toto jsou možné možné příčiny:

* Sady runbook se nemohou ověřovat se místní prostředky

* Hybridní pracovní proces je za proxy nebo brány firewall

* Sady runbook se nemohou ověřovat se místní prostředky

* Počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální požadavky na hardware.

#### <a name="resolution"></a>Řešení

Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na portu 443.

Počítače se systémem hybridní pracovní proces Runbooku by měl splňovat minimální požadavky na hardware před označením ho k hostování této funkce. V závislosti na využití prostředků jiné procesy na pozadí a kolizí způsobené sady runbook během provádění, jinak počítač stane přetížen a způsobit zpoždění úlohy sady runbook nebo vypršení časových limitů.

Potvrďte, že počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální požadavky na hardware. Pokud ano, sledujte využití procesoru a paměti k určení všech korelace mezi výkon procesů Hybrid Runbook Worker a Windows. Pokud je paměť nebo zatížení procesoru, může to znamenat potřeba upgradovat nebo přidáním dalších procesorů, případně zvyšte paměti adres problémové místo prostředků a opravte případné chyby. Nebo vyberte jiný výpočtový prostředek, který může podporovat minimální požadavky a škálování při vytížení indikovat, že se o zvýšení nezbytné.

Zkontrolujte **Microsoft SMA** v protokolu událostí je odpovídající událost s popis *Win32 proces skončil s kódem [4294967295]*. Příčina této chyby je nebyly nakonfigurované ověřování ve vašich sadách runbook nebo zadaná pověření spustit jako pro skupinu hybridních pracovních procesů. Zkontrolujte [oprávnění sady Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) potvrďte jste správně nakonfigurovali ověřování pro vaše sady runbook.

## <a name="linux"></a>Linux

Linux hybridní pracovní proces Runbook závisí na OMS agenta pro Linux komunikovat s vaším účtem Automation registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, zde jsou některé možné příčiny chyby:

###<a name="oms-agent-not-running"></a>Scénář: Není spuštěn Agent OMS pro Linux

Pokud není spuštěn Agent OMS pro Linux, nebude Linux Hybrid Runbook Worker komunikaci se službou Azure Automation. Ověření agenta se službou a zadáte následující příkaz: `ps -ef | grep python`. Měli byste vidět výstup podobný následujícímu procesy python s **nxautomation** uživatelský účet. Pokud nejsou povolena řešení správy aktualizací nebo Azure Automation, žádný z následujících postupů běží.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

V následujícím seznamu jsou procesy, které jsou spuštěny pro Linux Hybrid Runbook Worker. Všechny jsou umístěny v `/var/opt/microsoft/omsagent/state/automationworker/` adresáře.

* **OMS.conf** -Toto je správce pracovního procesu, to je spuštěna přímo z DSC.

* **Worker.conf** -tento proces je automaticky zaregistrován hybridní pracovní proces, je spuštěno pomocí Správce pracovního procesu. Tento proces slouží pomocí správy aktualizací a je pro uživatele transparentní. Tento proces se nenachází, pokud není na počítači povolená řešení pro správu aktualizací.

* **diy/Worker.conf** -tento proces je DIY hybridní pracovní proces. DIY hybridní pracovní proces se používá ke spouštění sad runbook uživatele na hybridní pracovní proces Runbooku. Liší se pouze z automatického zaregistrován hybridní pracovní proces podrobně klíče, který se používá jinou konfiguraci. Tento proces se nenachází, pokud řešení služby Azure Automation není povoleno a není registrovaný DIY Linux hybridní pracovní proces.

Pokud agenta OMS pro Linux neběží, spusťte následující příkaz pro spuštění služby: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

###<a name="class-does-not-exist"></a>Scénář: Pro zadanou třídu neexistuje.

Pokud se zobrazí následující chyba: **pro zadanou třídu neexistuje...** v `/var/opt/microsoft/omsconfig/omsconfig.log` pak OMS agenta pro Linux je nutné aktualizovat. Spusťte následující příkaz, který znovu nainstalujte agenta OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows hybridní pracovní proces Runbook závisí na agenta Microsoft Monitoring Agent komunikovat s vaším účtem Automation k registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, zde jsou některé možné příčiny chyby:

###<a name="mma-not-running"></a>Scénář: Microsoft Monitoring Agent není spuštěna.

#### <a name="issue"></a>Problém

`healthservice` Není spuštěná na počítači hybridní pracovní proces Runbooku.

#### <a name="cause"></a>Příčina

Jestliže není spuštěna služba Microsoft Monitoring Agent Windows, nebude hybridní pracovní proces Runbooku komunikaci se službou Azure Automation.

#### <a name="resolution"></a>Řešení

Ověřte agenta běží tak, že zadáte následující příkaz prostředí PowerShell: `Get-Service healthservice`. Pokud je služba zastavená, zadejte následující příkaz v prostředí PowerShell spustit službu: `Start-Service healthservice`.

###<a name="event-4502"></a> Událost 4502 v protokolu Operations Manager

#### <a name="issue"></a>Problém

V **aplikace a Správce služby Logs\Operations** protokolu událostí se zobrazí události 4502 a EventMessage obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**s následující popis: *certifikát předložený službu \<wsid\>. oms.opinsights.azure.com nevydala certifikační autorita používaná pro služby společnosti Microsoft. Obraťte se na správce sítě a zjistěte, zda používají proxy server, který zabrání komunikace TLS/SSL. V článku KB3126513 obsahuje další informace o připojení k řešení potíží.*

#### <a name="cause"></a>Příčina

Příčinou může být proxy serveru nebo síťové brány firewall blokuje komunikaci s Microsoft Azure. Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na porty 443.

#### <a name="resolution"></a>Řešení

Protokoly se ukládají místně na každém hybridní pracovní proces na C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Můžete zkontrolovat, zda existují jakékoli upozornění nebo chybové události zapsané do **aplikace a služby Logs\Microsoft-SMA\Operations** a **aplikace a Správce služby Logs\Operations** protokolu událostí může naznačovat připojení nebo jiné problém ovlivňující registrace role Azure Automation nebo problém při provádění operací se Normální.

[Runbook výstup a zprávy](../automation-runbook-output-and-messages.md) odešlou do Azure Automation z hybridní pracovní procesy stejně jako úlohy sady runbook běží v cloudu. Můžete také povolit podrobné a průběh datové proudy stejným způsobem jako pro ostatní sady runbook.

## <a name="next-steps"></a>Další postup

Pokud váš problém nenalezli nebo nemůžete vyřešit problém, navštíví některý z následujících kanály pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
