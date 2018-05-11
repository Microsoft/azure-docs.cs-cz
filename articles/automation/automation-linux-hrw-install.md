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
ms.openlocfilehash: 95f34e5d4fd966c41a30cc68c005237ae5405592
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
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
* Prostředí Power Shell

Následující typy runbook nefungují pro Linux Hybrid Worker:

* Pracovní postup prostředí PowerShell
* Grafické
* Pracovní postup grafické prostředí PowerShell

## <a name="next-steps"></a>Další postup

* Zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.
* Pokyny o tom, jak odebrat procesy Hybrid Runbook Worker najdete v tématu [odebrat Azure automatizace procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
