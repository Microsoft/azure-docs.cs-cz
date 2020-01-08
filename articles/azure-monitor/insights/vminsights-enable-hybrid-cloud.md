---
title: Povolit Azure Monitor (Preview) pro hybridní prostředí | Microsoft Docs
description: Tento článek popisuje, jak povolíte Azure Monitor pro virtuální počítače pro hybridní cloudové prostředí, které obsahuje jeden nebo víc virtuálních počítačů.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 40d89dd675e063283d1ed90cf145575b8164e4e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400693"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Povolit Azure Monitor pro virtuální počítače (Preview) pro hybridní prostředí

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (ve verzi Preview) pro virtuální počítače nebo fyzické počítače hostované ve vašem datovém centru nebo jiném cloudovém prostředí. Na konci tohoto procesu budete úspěšně začali monitorovat virtuální počítače ve vašem prostředí a zjistit, jestli se vyskytly problémy s výkonem nebo dostupností. 

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ověřte, že vaše předplatné a prostředky splňují požadavky. Seznamte se s požadavky a metody nasazení pro [agenta Log Analytics Linux a Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Azure Monitor pro agenta závislostí mapování virtuálních počítačů nebude přenášet vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Data mapy jsou vždy přenášena agentem Log Analytics do služby Azure Monitor, a to buď přímo, nebo prostřednictvím [brány Operations Management Suite](../../azure-monitor/platform/gateway.md) , pokud zásady zabezpečení IT nedovolují počítačům v síti připojení k Internetu.

Kroky pro dokončení této úlohy jsou shrnuté takto:

1. Nainstalujte agenta Log Analytics pro Windows nebo Linux. Než agenta nainstalujete, přečtěte si článek [Přehled agenta Log Analytics](../platform/log-analytics-agent.md) , který vám pomůže pochopit požadavky na systém a metody nasazení.

2. Stáhněte a nainstalujte Azure Monitor pro virtuální počítače mapu závislostí agenta pro [Windows](https://aka.ms/dependencyagentwindows) nebo [Linux](https://aka.ms/dependencyagentlinux).

3. Povolte shromažďování čítačů výkonu.

4. Nasazení pro virtuální počítače Azure Monitor.

>[!NOTE]
>Informace popsané v tomto článku pro nasazení agenta závislostí se vztahují také na [řešení Service map](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí na Windows

Můžete nainstalovat agenta závislostí ručně počítačích s Windows spuštěním `InstallDependencyAgent-Windows.exe`. Pokud spustíte tento spustitelný soubor bez jakýchkoli možností, spustí Průvodce instalací, zvoleném pro instalaci agenta interaktivně.

>[!NOTE]
>*Správce* instalace nebo odinstalace agenta jsou nutná oprávnění.

Následující tabulka obsahuje parametry, které podporují instalaci agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /S | Provádí tichou instalaci bez zásahu uživatele. |

Chcete-li například spustit instalační program s parametrem `/?`, zadejte **InstallDependencyAgent-Windows. exe/?** .

Soubory agenta závislostí Windows jsou nainstalované v *agenta závislostí C:\Program Files\Microsoft* ve výchozím nastavení. Pokud se agent závislostí nepovede spustit po dokončení instalace, podívejte se na protokoly, kde najdete podrobné informace o chybě. Adresář protokolu je *%Programfiles%\Microsoft závislost Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí v Linuxu

Agent závislostí je nainstalovaný na servery s Linuxem z *InstallDependencyAgent Linux64.bin*, skript prostředí samorozbalovací binárním souborem. Soubor můžete spustit pomocí `sh` nebo přidejte oprávnění k souboru, samotné.

>[!NOTE]
> K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.
>

| Parametr | Popis |
|:--|:--|
| – Nápověda | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| – Zkontrolujte | Kontrola oprávnění a operačního systému, ale nechcete nainstalovat agenta. |

Pokud například chcete spustit instalační program s parametrem `-help`, zadejte **InstallDependencyAgent-Linux64. bin-Help**.

Nainstalujte agenta pro Linux Dependencys jako kořenový adresář spuštěním příkazu `sh InstallDependencyAgent-Linux64.bin`.

Pokud agenta závislostí nespustí, zkontrolujte protokoly podrobné informace o chybě. U agentů Linuxu adresáři protokolu není */var/opt/microsoft/dependency-agent/log*.

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Příklady instalačního skriptu

Následující ukázkový skript je snadno nasazovat agenta závislosti na mnoha serverech najednou, k dispozici ke stažení a instalace agenta závislostí ve Windows nebo Linuxu.

### <a name="powershell-script-for-windows"></a>Skript PowerShellu pro Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Skript prostředí pro Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Požadovaná konfigurace stavu

Pokud chcete nasadit agenta závislostí pomocí Desired State Configuration (DSC), můžete použít modul xPSDesiredStateConfiguration pomocí následujícího ukázkového kódu:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Povolit čítače výkonu

Pokud pracovní prostor Log Analytics, který je odkazován řešení ještě nenakonfigurovala získat čítače výkonu, vyžadují řešení, musíte je povolit. Můžete to udělat jedním ze dvou způsobů:
* Ručně, jak je popsáno v [Windows a Linuxem zdroje dat výkonu do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a spuštěním skriptu PowerShellu, který je dostupný z [galerie Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Nasazení pro virtuální počítače Azure Monitor

Tato metoda zahrnuje šablony JSON, který určuje konfiguraci pro povolení součásti řešení ve vašem pracovním prostoru Log Analytics.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si téma:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud chcete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud chcete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Uložte soubor jako *installsolutionsforvminsights.json* do místní složky.

1. Zachyťte hodnoty pro pole *pracovní prostor*, *ResourceGroupName*a *WorkspaceLocation*. Hodnota pro název *pracovního prostoru* je název vašeho pracovního prostoru Log Analytics. Hodnota pro *WorkspaceLocation* je pracovní prostor je definována v oblasti.

1. Jste připraveni nasadit tuto šablonu pomocí následujícího příkazu Powershellu:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu, která zahrnuje výsledek:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav a metriky pro počítač hybridní.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="vm-doesnt-appear-on-the-map"></a>Virtuální počítač se na mapě nezobrazí.

Pokud se instalace agenta závislostí zdařila, ale váš počítač se na mapě nezobrazuje, Diagnostikujte problém pomocí následujících kroků.

1. Agent závislostí úspěšné instalaci? Můžete si to ověřit tak, že kontroluje se, pokud je služba nainstalována a spuštěna.

    **Windows**: Vyhledejte službu s názvem "Agent služby Microsoft Dependency." 

    **Linux**: oblast pro spouštění procesu "--agent služby microsoft dependency."

2. Jste na [cenové úrovni bezplatné Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Bezplatný plán umožňuje až pět jedinečných počítačů. Žádné následné počítače se na mapě nezobrazí, i když už předchozí pět neposílá data.

3. Odesílá počítač data protokolu a výkonu do Azure Monitor protokolů? Pro váš počítač proveďte následující dotaz: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Vrátil (a) jeden nebo více výsledků? Jsou data poslední? Pokud ano, Váš agent Log Analytics správně funguje a komunikuje se službou. Pokud ne, zkontrolujte agenta na serveru: [agenta Log Analytics pro řešení potíží s Windows](../platform/agent-windows-troubleshoot.md) nebo [agenta Log Analytics pro řešení potíží pro Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Počítač se zobrazí na mapě, ale nemá žádné procesy.

Pokud na mapě vidíte Server, ale nemá žádná data o procesu nebo připojení, která indikuje, že je agent závislostí nainstalovaný a spuštěný, ale ovladač jádra se nenačetl. 

Zkontrolujte soubor C:\Program Files\Microsoft závislost Agent\logs\wrapper.log (Windows) nebo soubor /var/opt/microsoft/dependency-agent/log/service.log (Linux). Poslední řádek souboru by měla zobrazovat, proč se nenačetla jádra. Například jádra nemusí být podporovány v Linuxu, pokud jste aktualizovali vaši jádra.


## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace k dispozici pro analýzu pomocí Azure Monitor pro virtuální počítače.
 
- Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).

- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).
