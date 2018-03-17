---
title: "Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu"
description: "Tento článek obsahuje informace o instalaci služby Azure Automation hybridní pracovní proces Runbooku umožňující spouštění sad runbook v počítačích se systémem Linux v místní datové centrum nebo cloudového prostředí."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: b68e8f7e67f767cff19e57f5864db89d6f059316
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Postup nasazení Linux Hybrid Runbook Worker

Runbooky ve službě Azure Automation nemají přístup k prostředkům v ostatních cloudů nebo v místním prostředí, protože spustit i v cloudu Azure. Funkce hybridní pracovní proces Runbooku automatizace Azure umožňuje spuštění sad runbook přímo na počítači, který hostuje roli a s prostředky v prostředí pro správu těchto místních prostředků. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači.

Tato funkce je znázorněno na následujícím obrázku:<br>  

![Přehled hybridních služby Runbook Worker](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Technický přehled role hybridní pracovní proces Runbooku, najdete v části [přehled architektury automatizace](automation-offering-get-started.md#automation-architecture-overview). Zkontrolujte následující informace ohledně [požadavky na hardware a software](automation-offering-get-started.md#hybrid-runbook-worker) a [informace pro přípravu síti](automation-offering-get-started.md#network-planning) než začnete nasazovat hybridní pracovní proces Runbooku. Po úspěšném nasazení služby runbook worker, zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.     

## <a name="hybrid-runbook-worker-groups"></a>Skupinám hybrid Runbook Worker
Každý hybridní pracovní proces Runbooku je členem skupiny hybridní pracovní proces Runbooku, který zadáte při instalaci agenta. Skupina může obsahovat jednoho agenta, ale můžete nainstalovat více agentů ve skupině pro vysokou dostupnost.

Při spuštění sady runbook pro Hybrid Runbook Worker, určete skupinu, která běží na. Členové skupiny určit, které pracovní obsluhuje požadavek. Nelze zadat konkrétní pracovního procesu.

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalace Linux hybridní pracovní proces Runbooku
K instalaci a konfiguraci Hybrid Runbook Worker ve vašem počítači Linux, postupujte podle dál přímo proces ručně nainstalujte a nakonfigurujte roli. Vyžaduje povolení **Automation Hybrid Worker** řešení v vaším pracovním prostorem OMS a pak spustit sadu příkazů pro registraci počítače jako pracovní proces a přidejte ji do nové nebo existující skupiny. 

Než budete pokračovat, musíte svůj účet Automation je spojena se pracovní prostor analýzy protokolů a také primární klíč pro svůj účet Automation. Obě z portálu můžete najít a vyberete svůj účet Automation a vyberete **prostoru** pro dané ID pracovního prostoru a výběr **klíče** pro primární klíč.  

1.  Povolte řešení "Automation Hybrid Worker" v OMS. To můžete provést buď:

   1. Z Galerie řešení v [portálu OMS](https://mms.microsoft.com), povolte **Automation Hybrid Worker** řešení
   2. Spusťte následující rutinu:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Spusťte následující příkaz, změna hodnot pro parametry *-w*, *-k*, *-g*, a *-e*. Pro *-g* parametr nahraďte název skupiny hybridní pracovní proces Runbooku, které se musí připojit nový Linux Hybrid Runbook Worker hodnotu. Pokud název již neexistuje v účtu Automation, nové skupiny hybridní pracovní proces Runbooku se provádí s tímto názvem.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Po dokončení příkazu v okně skupinám Hybrid Worker na portálu Azure se zobrazí nové skupiny a počet členů, nebo pokud existující skupiny, se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupinám Hybrid Worker** a vyberte **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** okně uvidíte každého člena skupiny uvedené.  


## <a name="turning-off-signature-validation"></a>Vypnutí ověřování podpisu 
Ve výchozím nastavení Linux hybridní pracovní procesy Runbooku vyžadovat ověření podpisu. Pokud spustíte nepodepsaný runbook proti pracovní, zobrazí se chyba obsahující "Podpis ověření se nezdařilo". Chcete-li vypnout ověřování podpisu, spusťte následující příkaz, nahraďte druhý parametr vaše ID pracovního prostoru OMS:

    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <OMSworkspaceId>
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

## <a name="next-steps"></a>Další postup

* Zkontrolujte [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md) se dozvíte, jak nakonfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí.
* Pokyny o tom, jak odebrat procesy Hybrid Runbook Worker najdete v tématu [odebrat Azure automatizace procesů Hybrid Runbook Worker](automation-remove-hrw.md)
