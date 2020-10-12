---
title: Nasazení Hybrid Runbook Worker pro Linux v Azure Automation
description: V tomto článku se dozvíte, jak nainstalovat Azure Automation Hybrid Runbook Worker ke spouštění Runbooků na počítačích se systémem Linux v místním datovém centru nebo v cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 8295b6bba9703c276bf60a0360ded6f0e195369e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776268"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker pro Linux

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo na počítači, který je hostitelem role a k prostředkům v prostředí za účelem správy těchto místních prostředků. Linux Hybrid Runbook Worker spouští Runbooky jako speciálního uživatele, který může být zvýšen na spouštění příkazů, které vyžadují zvýšení úrovně oprávnění. Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více určených počítačů. Tento článek popisuje, jak nainstalovat Hybrid Runbook Worker do počítače se systémem Linux, jak odebrat pracovní proces a jak odebrat skupinu Hybrid Runbook Worker.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

### <a name="a-log-analytics-workspace"></a>Pracovní prostor Log Analytics

Role Hybrid Runbook Worker závisí na pracovním prostoru Azure Monitor Log Analytics k instalaci a konfiguraci role. Můžete ji vytvořit prostřednictvím [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), prostřednictvím [PowerShellu](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)nebo v [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Pokud nemáte pracovní prostor Azure Monitor Log Analytics, před vytvořením pracovního prostoru si přečtěte téma [Průvodce návrhem protokolu Azure monitor](../azure-monitor/platform/design-logs-deployment.md) .

Pokud máte pracovní prostor, ale není propojený s vaším účtem Automation, umožňuje funkce automatizace přidat funkce pro Azure Automation, včetně podpory Hybrid Runbook Worker. Pokud povolíte jednu z Azure Automation funkcí v pracovním prostoru Log Analytics, konkrétně [Update Management](update-management/update-mgmt-overview.md) nebo [Change Tracking a inventáře](change-tracking.md), automaticky se do počítače agenta přiřadí komponenty pracovního procesu.

Pokud chcete přidat funkci Update Management do svého pracovního prostoru, spusťte následující rutinu PowerShellu:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

Pokud chcete do svého pracovního prostoru přidat funkci Change Tracking a inventáře, spusťte následující rutinu PowerShellu:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agent Log Analytics

Role Hybrid Runbook Worker vyžaduje, aby byl pro podporovaný operační systém Linux [Log Analytics agent](../azure-monitor/platform/log-analytics-agent.md) .

>[!NOTE]
>Po instalaci agenta Log Analytics pro Linux byste neměli měnit oprávnění ke `sudoers.d` složce nebo jejímu vlastnictví. Pro účet **nxautomation** se vyžaduje oprávnění sudo, což je kontext uživatele, ke kterému se Hybrid Runbook Worker spouští. Oprávnění by se neměla odebírat. Omezení tohoto omezení na určité složky nebo příkazy může mít za následek zásadní změnu.
>

### <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Funkce Hybrid Runbook Worker podporuje následující distribuce:

* Amazon Linux 2012,09 až 2015,09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS a 18,04 (x86/x64)
* SUSE Linux Enterprise Server 12 (x86/x64)

### <a name="minimum-requirements"></a>Minimální požadavky

Minimální požadavky pro Hybrid Runbook Worker pro Linux jsou:

* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovna GNU C| 2.5-12 |
|Openssl| Knihovny OpenSSL | 1,0 (podporované TLS 1,1 a TLS 1,2)|
|Curl | Webový klient s kudrlinkou | 7.15.5|
|Python – ctypes | Python 2. x je povinný. |
|PAM | Pluggable Authentication Modules|
| **Volitelný balíček** | **Popis** | **Minimální verze**|
| PowerShell Core | Aby bylo možné spouštět Runbooky PowerShellu, je nutné nainstalovat prostředí PowerShell Core. Další informace o tom, jak ji nainstalovat, najdete v tématu [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) . | 6.0.0 |

## <a name="supported-linux-hardening"></a>Podporované posílení zabezpečení pro Linux

Následující nejsou dosud podporovány:

* SLUŽBY

## <a name="supported-runbook-types"></a>Podporované typy runbooků

Procesy Hybrid Runbook Worker pro Linux podporují v Azure Automation omezené sady sad Runbook a jsou popsány v následující tabulce.

|Typ Runbooku | Podporováno |
|-------------|-----------|
|Python 2 |Yes |
|PowerShell |Ano<sup>1</sup> |
|Pracovní postup PowerShellu |No |
|Grafický |No |
|Grafický pracovní postup PowerShellu |No |

<sup>1</sup> Runbooky PowerShellu vyžadují, aby byl na počítači se systémem Linux nainstalovaný PowerShell Core. Další informace o tom, jak ji nainstalovat, najdete v tématu [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) .

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalace Hybrid Runbook Worker pro Linux

Pokud chcete nainstalovat a nakonfigurovat Hybrid Runbook Worker pro Linux, proveďte následující kroky.

1. Nasaďte agenta Log Analytics do cílového počítače.

    * Pro virtuální počítače Azure nainstalujte agenta Log Analytics pro Linux pomocí [rozšíření virtuálního počítače pro Linux](../virtual-machines/extensions/oms-linux.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics pomocí šablony Azure Resource Manager nebo rozhraní příkazového řádku Azure CLI. Po instalaci agenta je možné virtuální počítač přidat do skupiny Hybrid Runbook Worker v účtu Automation.

    * V případě virtuálních počítačů mimo Azure nainstalujte Log Analytics agenta pro Linux pomocí možností nasazení popsaných v tématu [připojení počítačů se systémem Linux k Azure monitor](../azure-monitor/platform/agent-linux.md) článku. Tento postup můžete opakovat, pokud chcete do vašeho prostředí přidat více pracovních procesů. Po instalaci agenta je možné virtuální počítače přidat do skupiny Hybrid Runbook Worker v účtu Automation.

    > [!NOTE]
    > Chcete-li spravovat konfiguraci počítačů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné přidat počítače jako uzly DSC.

    > [!NOTE]
    > Během instalace Hybrid Worker pro Linux musí být přítomen [účet nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) s odpovídajícími oprávněními sudo. Pokud se pokusíte nainstalovat pracovní proces a účet není přítomen nebo nemáte příslušná oprávnění, instalace se nezdařila.

2. Ověřte, že agent hlásí do pracovního prostoru.

    Agent Log Analytics pro Linux připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do vašeho počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřebné pro Hybrid Runbook Worker.

    Jakmile se agent úspěšně připojí k pracovnímu prostoru Log Analytics po několika minutách, můžete spustit následující dotaz, který ověří, jestli odesílá data prezenčního signálu do pracovního prostoru.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro daný počítač, což znamená, že je připojený a oznamuje službě zprávy. Ve výchozím nastavení každý Agent přepošle záznam prezenčního signálu do přiřazeného pracovního prostoru.

3. Spuštěním následujícího příkazu přidejte počítač do skupiny Hybrid Runbook Worker a zadejte hodnoty parametrů `-w` ,, `-k` `-g` a `-e` .

    Můžete získat informace požadované pro parametry `-k` a `-e` ze stránky **klíče** ve vašem účtu Automation. V části **Nastavení účtu** na levé straně stránky vyberte **klíče** .

    ![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Pro `-e` parametr Zkopírujte hodnotu pro **URL**.

    * Pro `-k` parametr Zkopírujte hodnotu **primárního přístupového klíče**.

    * Pro `-g` parametr zadejte název skupiny Hybrid Runbook Worker, ke které se má připojit nový hybrid Runbook Worker pro Linux. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, vytvoří se s tímto názvem.

    * Pro `-w` parametr zadejte ID vašeho pracovního prostoru Log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. Po dokončení příkazu se na stránce Hybrid Worker skupiny v účtu Automation zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce Hybrid Worker skupiny a vybrat dlaždici **hybridní pracovní procesy** . Na stránce hybridní procesy se zobrazí všichni členové skupiny v seznamu.

    > [!NOTE]
    > Pokud pro virtuální počítač Azure používáte rozšíření Log Analytics virtuálních počítačů pro Linux, doporučujeme, `autoUpgradeMinorVersion` aby nastavení na `false` Automatické upgradování verzí mohlo způsobovat problémy s Hybrid Runbook Worker. Informace o tom, jak rozšíření upgradovat ručně, najdete v tématu nasazení rozhraní příkazového [řádku Azure CLI](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Vypnout ověřování podpisů

Ve výchozím nastavení vyžadují procesy Hybrid Runbook Worker pro Linux ověření podpisu. Pokud spustíte nepodepsaný Runbook s pracovníkem, zobrazí se `Signature validation failed` Chyba. Pokud chcete vypnout ověřování podpisů, spusťte následující příkaz. Nahraďte druhý parametr ID vašeho pracovního prostoru Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Odebrání Hybrid Runbook Worker z místního počítače se systémem Linux

`ls /var/opt/microsoft/omsagent`K získání ID pracovního prostoru můžete použít příkaz na Hybrid Runbook Worker. Vytvoří se složka s názvem ID pracovního prostoru.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Tento skript neodebere z počítače agenta Log Analytics pro Linux. Odebírá jenom funkce a konfiguraci role Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Odebrání skupiny Hybrid Worker

Chcete-li odebrat Hybrid Runbook Worker skupinu počítačů se systémem Linux, použijte stejný postup jako u skupiny hybridních pracovních procesů systému Windows. Viz [Odebrání skupiny Hybrid Worker](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Informace o tom, jak řešit potíže s procesy Hybrid Runbook Worker, najdete v tématu [řešení potíží s Hybrid Runbook Worker problémy – Linux](troubleshoot/hybrid-runbook-worker.md#linux).
