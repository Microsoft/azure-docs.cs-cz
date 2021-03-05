---
title: Nasazení Hybrid Runbook Worker pro Linux v Azure Automation
description: V tomto článku se dozvíte, jak nainstalovat Azure Automation Hybrid Runbook Worker ke spouštění Runbooků na počítačích se systémem Linux v místním datovém centru nebo v cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: d4d9bcd16e36e76808f19f7fbd43dd0d3e7550c3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182328"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker pro Linux

Pomocí funkce User Hybrid Runbook Worker of Azure Automation můžete spouštět Runbooky přímo na počítačích Azure nebo mimo Azure, včetně serverů zaregistrovaných u [serverů s podporou ARC Azure](../azure-arc/servers/overview.md). Z počítače nebo serveru, který je hostitelem role, můžete spouštět Runbooky přímo a s prostředky v prostředí pro správu těchto místních prostředků.

Linux Hybrid Runbook Worker spouští Runbooky jako speciálního uživatele, který může být zvýšen na spouštění příkazů, které vyžadují zvýšení úrovně oprávnění. Azure Automation ukládá a spravuje Runbooky a pak je doručí do jednoho nebo více určených počítačů. Tento článek popisuje, jak nainstalovat Hybrid Runbook Worker do počítače se systémem Linux, jak odebrat pracovní proces a jak odebrat skupinu Hybrid Runbook Worker.

Po úspěšném nasazení služby Runbook Worker si přečtěte téma [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md) , kde se dozvíte, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující.

### <a name="a-log-analytics-workspace"></a>Pracovní prostor Log Analytics

Role Hybrid Runbook Worker závisí na pracovním prostoru Azure Monitor Log Analytics k instalaci a konfiguraci role. Můžete ji vytvořit prostřednictvím [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), prostřednictvím [PowerShellu](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)nebo v [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Pokud nemáte pracovní prostor Azure Monitor Log Analytics, před vytvořením pracovního prostoru si přečtěte téma [Průvodce návrhem protokolu Azure monitor](../azure-monitor/logs/design-logs-deployment.md) .

### <a name="log-analytics-agent"></a>Agent Log Analytics

Role Hybrid Runbook Worker vyžaduje, aby byl pro podporovaný operační systém Linux [Log Analytics agent](../azure-monitor/agents/log-analytics-agent.md) . U serverů nebo počítačů hostovaných mimo Azure můžete agenta Log Analytics nainstalovat pomocí [serverů s podporou ARC Azure](../azure-arc/servers/overview.md).

>[!NOTE]
>Po instalaci agenta Log Analytics pro Linux byste neměli měnit oprávnění ke `sudoers.d` složce nebo jejímu vlastnictví. Pro účet **nxautomation** se vyžaduje oprávnění sudo, což je kontext uživatele, ke kterému se Hybrid Runbook Worker spouští. Oprávnění by se neměla odebírat. Omezení tohoto omezení na určité složky nebo příkazy může mít za následek zásadní změnu.
>

### <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Funkce Hybrid Runbook Worker podporuje následující distribuce. Všechny operační systémy se považují za x64. Platforma x86 není podporována pro žádný operační systém.

* Amazon Linux 2012,09 až 2015,09
* CentOS Linux 5, 6, 7 a 8
* Oracle Linux 5, 6 a 7
* Red Hat Enterprise Linux Server 5, 6, 7 a 8
* Debian GNU/Linux 6, 7 a 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS a 18,04 LTS
* SUSE Linux Enterprise Server 12 a 15 (SUSE neobsahují verze s číslem 13 nebo 14)

> [!IMPORTANT]
> Než povolíte funkci Update Managemente, která závisí na roli systému Hybrid Runbook Worker, potvrďte [níže](update-management/overview.md#supported-operating-systems)podporované distribuce.

### <a name="minimum-requirements"></a>Minimální požadavky

Minimální požadavky pro systém Linux a uživatelské Hybrid Runbook Worker jsou:

* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovna GNU C| 2.5-12 |
|Openssl| Knihovny OpenSSL | 1,0 (podporované TLS 1,1 a TLS 1,2)|
|Curl | Webový klient s kudrlinkou | 7.15.5|
|Python – ctypes | Vyžaduje se Python 2. x nebo Python 3. x. |
|PAM | Pluggable Authentication Modules|
| **Volitelný balíček** | **Popis** | **Minimální verze**|
| PowerShell Core | Aby bylo možné spouštět Runbooky PowerShellu, je nutné nainstalovat prostředí PowerShell Core. Další informace o tom, jak ji nainstalovat, najdete v tématu [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) . | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Přidání počítače do skupiny Hybrid Runbook Worker

Pracovní počítač můžete přidat do skupiny Hybrid Runbook Worker v jednom z vašich účtů Automation. Pro počítače, které hostují systém Hybrid Runbook Worker spravované pomocí Update Management, je lze přidat do skupiny Hybrid Runbook Worker. Je ale nutné použít stejný účet Automation pro Update Management a členství v Hybrid Runbook Worker skupině.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automaticky nainstaluje systémovou Hybrid Runbook Worker na počítač s Azure nebo mimo Azure, který je povolený pro Update Management. Tento pracovní proces ale není zaregistrovaný u žádné skupiny Hybrid Runbook Worker ve vašem účtu Automation. Pokud chcete spouštět Runbooky na těchto počítačích, musíte je přidat do skupiny Hybrid Runbook Worker. Postupujte podle kroku 4 v části [instalace Hybrid Runbook Worker pro Linux](#install-a-linux-hybrid-runbook-worker) a přidejte ji do skupiny.

## <a name="supported-linux-hardening"></a>Podporované posílení zabezpečení pro Linux

Následující nejsou dosud podporovány:

* SLUŽBY

## <a name="supported-runbook-types"></a>Podporované typy runbooků

Procesy Hybrid Runbook Worker pro Linux podporují v Azure Automation omezené sady sad Runbook a jsou popsány v následující tabulce.

|Typ Runbooku | Podporováno |
|-------------|-----------|
|Python 3 (Preview)|Ano, požadováno jenom pro tyto distribuce: SUSE LES 15, RHEL 8 a CentOS 8|
|Python 2 |Ano, pro všechny distribuce, které nevyžadují Python 3<sup>1</sup> |
|PowerShell |Ano<sup>2</sup> |
|Pracovní postup PowerShellu |Ne |
|Grafický |Ne |
|Grafický pracovní postup PowerShellu |Ne |

<sup>1</sup> Viz [podporované operační systémy Linux](#supported-linux-operating-systems).

<sup>2</sup> . Runbooky PowerShellu vyžadují, aby byl na počítači se systémem Linux nainstalovaný PowerShell Core. Další informace o tom, jak ji nainstalovat, najdete v tématu [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) .

### <a name="network-configuration"></a>Konfigurace sítě

Požadavky na síť pro Hybrid Runbook Worker najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalace Hybrid Runbook Worker pro Linux

Pokud chcete nainstalovat a nakonfigurovat Hybrid Runbook Worker pro Linux, proveďte následující kroky.

1. Povolte řešení Azure Automation v pracovním prostoru Log Analytics spuštěním následujícího příkazu v příkazovém řádku PowerShellu se zvýšenými oprávněními nebo v Cloud Shell v [Azure Portal](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Nasaďte agenta Log Analytics do cílového počítače.

    * Pro virtuální počítače Azure nainstalujte agenta Log Analytics pro Linux pomocí [rozšíření virtuálního počítače pro Linux](../virtual-machines/extensions/oms-linux.md). Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje virtuální počítače do existujícího pracovního prostoru Log Analytics. Pomocí šablony Azure Resource Manager, rozhraní příkazového řádku Azure nebo Azure Policy můžete přiřadit předdefinované zásady [nasazení Log Analytics agenta pro virtuální počítače se *systémem* *Linux* nebo Windows](../governance/policy/samples/built-in-policies.md#monitoring) . Po instalaci agenta je možné počítač přidat do skupiny Hybrid Runbook Worker v účtu Automation.

    * U počítačů mimo Azure můžete agenta Log Analytics nainstalovat pomocí [serverů s podporou ARC Azure](../azure-arc/servers/overview.md). Servery s podporou ARC podporují nasazení Log Analytics agenta pomocí následujících metod:

        - Pomocí architektury rozšíření virtuálních počítačů.

            Tato funkce na serverech s podporou ARC Azure umožňuje nasadit rozšíření virtuálního počítače Log Analytics agenta do jiného serveru než Azure s Windows nebo Linux. Rozšíření virtuálních počítačů je možné spravovat pomocí následujících metod na hybridních počítačích nebo serverech spravovaných servery s podporou ARC:

            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Rozhraní příkazového [řádku Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Šablony Azure správce prostředků](../azure-arc/servers/manage-vm-extensions-template.md)

        - Použití Azure Policy.

            Pomocí tohoto přístupu použijete Azure Policy [nasadit Log Analytics agenta do systému Linux nebo integrované zásady Windows Azure ARC](../governance/policy/samples/built-in-policies.md#monitoring) pro audit, pokud je na serveru s povoleným Arc agent Log Analytics nainstalován. Pokud není agent nainstalovaný, automaticky ho nasadí pomocí úlohy nápravy. Případně, pokud plánujete monitorovat počítače pomocí Azure Monitor pro virtuální počítače, místo toho použijte k instalaci a konfiguraci agenta Log Analytics [možnost povolit Azure monitor pro virtuální počítače](../governance/policy/samples/built-in-initiatives.md#monitoring) .

        K instalaci agenta Log Analytics pro Windows nebo Linux doporučujeme použít Azure Policy.

    > [!NOTE]
    > Chcete-li spravovat konfiguraci počítačů, které podporují roli Hybrid Runbook Worker s požadovaným stavem konfigurace (DSC), je nutné přidat počítače jako uzly DSC.

    > [!NOTE]
    > Během instalace Hybrid Worker pro Linux musí být přítomen [účet nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) s odpovídajícími oprávněními sudo. Pokud se pokusíte nainstalovat pracovní proces a účet není přítomen nebo nemáte příslušná oprávnění, instalace se nezdařila.

3. Ověřte, že agent hlásí do pracovního prostoru.

    Agent Log Analytics pro Linux připojuje počítače k pracovnímu prostoru Azure Monitor Log Analytics. Když nainstalujete agenta do vašeho počítače a připojíte ho k pracovnímu prostoru, automaticky stáhne součásti, které jsou potřebné pro Hybrid Runbook Worker.

    Jakmile se agent úspěšně připojí k pracovnímu prostoru Log Analytics po několika minutách, můžete spustit následující dotaz, který ověří, jestli odesílá data prezenčního signálu do pracovního prostoru.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Ve výsledcích hledání byste měli vidět záznamy prezenčního signálu pro daný počítač, což znamená, že je připojený a oznamuje službě zprávy. Ve výchozím nastavení každý Agent přepošle záznam prezenčního signálu do přiřazeného pracovního prostoru.

4. Spuštěním následujícího příkazu přidejte počítač do skupiny Hybrid Runbook Worker a zadejte hodnoty parametrů `-w` ,, `-k` `-g` a `-e` .

    Můžete získat informace požadované pro parametry `-k` a `-e` ze stránky **klíče** ve vašem účtu Automation. V části **Nastavení účtu** na levé straně stránky vyberte **klíče** .

    ![Stránka Správa klíčů](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Pro `-e` parametr Zkopírujte hodnotu pro **URL**.

    * Pro `-k` parametr Zkopírujte hodnotu **primárního přístupového klíče**.

    * Pro `-g` parametr zadejte název skupiny Hybrid Runbook Worker, ke které se má připojit nový hybrid Runbook Worker pro Linux. Pokud tato skupina už v účtu Automation existuje, do ní se přidá aktuální počítač. Pokud tato skupina neexistuje, vytvoří se s tímto názvem.

    * Pro `-w` parametr zadejte ID vašeho pracovního prostoru Log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Ověřte nasazení po dokončení skriptu. Na stránce **Hybrid Runbook Worker skupiny** ve vašem účtu Automation na kartě **Skupina hybridních pracovních procesů Runbooku uživatele** se zobrazí nová nebo existující skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce. v nabídce vlevo zvolte **hybridní pracovní procesy**. Na stránce **hybridní pracovní procesy** uvidíte všechny členy uvedené skupiny.

    > [!NOTE]
    > Pokud pro virtuální počítač Azure používáte rozšíření Log Analytics virtuálních počítačů pro Linux, doporučujeme, `autoUpgradeMinorVersion` aby nastavení na `false` Automatické upgradování verzí mohlo způsobovat problémy s Hybrid Runbook Worker. Informace o tom, jak rozšíření upgradovat ručně, najdete v tématu nasazení rozhraní příkazového [řádku Azure CLI](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Vypnout ověřování podpisů

Ve výchozím nastavení vyžadují procesy Hybrid Runbook Worker pro Linux ověření podpisu. Pokud spustíte nepodepsaný Runbook s pracovníkem, zobrazí se `Signature validation failed` Chyba. Pokud chcete vypnout ověřování podpisů, spusťte následující příkaz. Nahraďte druhý parametr ID vašeho pracovního prostoru Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Odebrat Hybrid Runbook Worker

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