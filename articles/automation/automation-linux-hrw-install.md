---
title: Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu
description: Tento článek obsahuje informace o instalaci služby Azure Automation hybridní pracovní proces Runbooku umožňující spouštění sad runbook v počítačích se systémem Linux v místní datové centrum nebo cloudového prostředí.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e95f5d585fa97a62b709e73b6ed6eacafe69a2b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Postup nasazení Linux Hybrid Runbook Worker

Funkce hybridní pracovní proces Runbooku automatizace Azure umožňuje spuštění sad runbook přímo na počítači, který hostuje roli a s prostředky v prostředí pro správu těchto místních prostředků. Linux Hybrid Runbook Worker spustí sady runbook jako zvláštního uživatele, který může být zvýšena pro spouštění příkazů, které je třeba zvýšení oprávnění. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači. To článku je popsáno, jak nainstalovat hybridní pracovní proces Runbooku na počítač s Linuxem.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Následuje seznam distribucí Linux, které jsou podporovány:

* Amazon Linux 2012.09--> 2015.09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5,6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux rozlehlé sítě serveru 11 a 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalace Linux hybridní pracovní proces Runbooku

K instalaci a konfiguraci Hybrid Runbook Worker ve vašem počítači Linux, postupujte podle dál přímo proces ručně nainstalujte a nakonfigurujte roli. Vyžaduje povolení **Automation Hybrid Worker** řešení v pracovní prostor analýzy protokolů a pak spustit sadu příkazů pro registraci počítače jako pracovní proces a přidejte ji do nové nebo existující skupiny.

Toto jsou minimální požadavky pro Linux Hybrid Runbook Worker:

* Minimálně dvě jádra
* Minimálně 4 GB paměti RAM
* Port 443 (odchozí)

### <a name="package-requirements"></a>Požadavky na balíček

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovny jazyka C GNU| 2.5-12 |
|OpenSSL| Knihovny OpenSSL | 0.9.8E nebo 1.0|
|Curl | cURL webového klienta | 7.15.5|
|Python ctypes | |
|PAM | Modulů PAM|

Než budete pokračovat, musíte svůj účet Automation je spojena se pracovní prostor analýzy protokolů a také primární klíč pro svůj účet Automation. Obě z portálu můžete najít a vyberete svůj účet Automation a vyberete **prostoru** pro dané ID pracovního prostoru a výběr **klíče** pro primární klíč. Informace o porty a adresy, které jsou potřebné pro hybridní pracovní proces Runbooku, najdete v části [konfigurace vaší sítě](automation-hybrid-runbook-worker.md#network-planning).

1. Povolte "Automation Hybrid Worker" řešení v Azure. To můžete provést buď:

   1. Přidat **Automation Hybrid Worker** řešení k předplatnému pomocí postupu v [řešení pro správu přidat analýzy protokolů do pracovního prostoru](../log-analytics/log-analytics-add-solutions.md).
   1. Spusťte následující rutinu:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Nainstalovat agenta OMS pro Linux tak, že spustíte následující příkaz, nahraďte \<WorkspaceID\> a \<WorkspaceKey\> s příslušnými hodnotami z pracovního prostoru.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Spusťte následující příkaz, změna hodnot pro parametry *-w*, *-k*, *-g*, a *-e*. Pro *-g* parametr, nahraďte název skupiny hybridní pracovní proces Runbooku, které se musí připojit nový Linux Hybrid Runbook Worker hodnotu. Pokud název již neexistuje v účtu Automation, nové skupiny hybridní pracovní proces Runbooku se provádí s tímto názvem.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po dokončení příkazu skupinám Hybrid Worker stránky na portálu Azure se zobrazí nové skupiny a počet členů nebo existující skupiny, pokud se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupinám Hybrid Worker** a vyberte **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** stránky, uvidíte každého člena skupiny uvedené.

## <a name="turning-off-signature-validation"></a>Vypnutí ověřování podpisu

Ve výchozím nastavení Linux hybridní pracovní procesy Runbooku vyžadovat ověření podpisu. Pokud spustíte nepodepsaný runbook proti pracovní, zobrazí se chyba obsahující "Podpis ověření se nezdařilo". Chcete-li vypnout ověřování podpisu, spusťte následující příkaz, nahraďte druhý parametr vaše ID pracovního prostoru analýzy protokolů:

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy podporované runbooků

Linux hybridní pracovní procesy Runbooku nepodporují úplnou sadu runbook typy, které se nacházejí v rámci Azure automation.

Následující typy runbook pracovat na hybridní pracovní proces Linux:

* Python 2
* PowerShell

Následující typy runbook nefungují pro Linux Hybrid Worker:

* Pracovní postup prostředí PowerShell
* Grafické
* Pracovní postup grafické prostředí PowerShell

## <a name="troubleshooting"></a>Řešení potíží

Linux hybridní pracovní proces Runbook závisí na OMS agenta pro Linux komunikovat s vaším účtem Automation registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, zde jsou některé možné příčiny chyby:

### <a name="the-oms-agent-for-linux-is-not-running"></a>Není spuštěn Agent OMS pro Linux

Pokud není spuštěn Agent OMS pro Linux, nebude Linux Hybrid Runbook Worker komunikaci se službou Azure Automation. Ověření agenta se službou a zadáte následující příkaz: `ps -ef | grep python`. Měli byste vidět výstup podobný následujícímu procesy python s **nxautomation** uživatelský účet. Pokud nejsou povolena řešení správy aktualizací nebo Azure Automation, žádný z těchto procesů bude spuštěna.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

V následujícím seznamu jsou procesy, které jsou spuštěny pro Linux Hybrid Runbook Worker. Všechny jsou umístěny v `/var/opt/microsoft/omsagent/state/automationworker/` adresáře.

* **OMS.conf** -Toto je správce pracovního procesu, to je spuštěna přímo z DSC.

* **Worker.conf** -tento proces je automaticky zaregistrován hybridní pracovní proces, je spuštěno pomocí Správce pracovního procesu. Tento proces slouží pomocí správy aktualizací a je pro uživatele transparentní. Tento proces není být k dispozici, pokud není na počítači povolená řešení pro správu aktualizací.

* **diy/Worker.conf** -tento proces je DIY hybridní pracovní proces. DIY hybridní pracovní proces se používá ke spouštění sad runbook uživatele na hybridní pracovní proces Runbooku. Liší se pouze z automatického zaregistrován hybridní pracovní proces podrobně klíče, který se používá jinou konfiguraci. Tento proces není přítomen Azure Automation není povoleno řešení a DIY hybridní pracovní proces Linux, není-li zaregistrovat.

Pokud agenta OMS pro Linux neběží, spusťte následující příkaz pro spuštění služby: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-does-not-exist"></a>Pro zadanou třídu neexistuje.

Pokud se zobrazí chyba **pro zadanou třídu neexistuje...** v `/var/opt/microsoft/omsconfig/omsconfig.log` pak OMS agenta pro Linux je nutné aktualizovat. Spusťte následující příkaz, který znovu nainstalovat agenta OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Další pokyny o tom, jak vyřešit problémy s správy aktualizací najdete v tématu [správy aktualizací – řešení potíží](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Další postup

* Zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.
* Pokyny o tom, jak odebrat procesy Hybrid Runbook Worker najdete v tématu [odebrat Azure automatizace procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
