---
title: Povolení Azure Monitor pro hybridní prostředí | Microsoft Docs
description: Tento článek popisuje, jak povolíte Azure Monitor pro virtuální počítače pro hybridní cloudové prostředí, které obsahuje jeden nebo víc virtuálních počítačů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 013515e0608bf790ceef8dc13d9d547496306610
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092843"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Povolení Azure Monitor pro virtuální počítače pro hybridní prostředí

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítače nebo fyzické počítače hostované ve vašem datovém centru nebo jiném cloudovém prostředí. Na konci tohoto procesu budete úspěšně začali monitorovat virtuální počítače ve vašem prostředí a zjistit, jestli se vyskytly problémy s výkonem nebo dostupností.

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ověřte, že vaše předplatné a prostředky splňují požadavky. Přečtěte si požadavky a metody nasazení pro [Log Analytics Linux a Windows Agent](../platform/log-analytics-agent.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Agent závislostí Azure Monitor pro virtuální počítače neodesílá žádná data a nevyžaduje žádné změny bran firewall nebo portů. Data mapy jsou vždy přenášena agentem Log Analytics do služby Azure Monitor, a to buď přímo, nebo prostřednictvím [brány Operations Management Suite](../../azure-monitor/platform/gateway.md) , pokud zásady zabezpečení IT nedovolují počítačům v síti připojení k Internetu.

Kroky pro dokončení této úlohy jsou shrnuté takto:

1. Nainstalujte agenta Log Analytics pro Windows nebo Linux. Než agenta nainstalujete, přečtěte si článek [Přehled agenta Log Analytics](../platform/log-analytics-agent.md) , který vám pomůže pochopit požadavky na systém a metody nasazení.

2. Stáhněte a nainstalujte agenta Azure Monitor pro virtuální počítačech závislostí mapy pro [Windows](https://aka.ms/dependencyagentwindows) nebo [Linux](https://aka.ms/dependencyagentlinux).

3. Povolte shromažďování čítačů výkonu.

4. Nasazení Azure Monitor pro virtuální počítače.

>[!NOTE]
>Informace popsané v tomto článku pro nasazení agenta závislostí se vztahují také na [řešení Service map](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí ve Windows

Agenta závislostí můžete nainstalovat ručně do počítačů se systémem Windows `InstallDependencyAgent-Windows.exe` . Pokud tento spustitelný soubor spustíte bez jakýchkoli možností, spustí se Průvodce instalací nástroje, který můžete použít k interaktivní instalaci agenta.

>[!NOTE]
>K instalaci nebo odinstalaci agenta jsou nutná oprávnění *správce* .

Následující tabulka popisuje parametry, které jsou podporovány instalačním programem pro agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| Parametr | Provede tichou instalaci bez zásahu uživatele. |

Pokud třeba chcete spustit instalační program s `/?` parametrem, zadejte **InstallDependencyAgent-Windows.exe/?**.

Soubory pro agenta závislostí Windows se ve výchozím nastavení instalují do složky *C:\Program Files\Microsoft Dependency agent* . Pokud se agent závislostí nepovede spustit po dokončení instalace, podívejte se na protokoly, kde najdete podrobné informace o chybě. Adresář protokolu je *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí na Linux

Agent závislostí je nainstalovaný na serverech se systémem Linux z *InstallDependencyAgent-Linux64. bin*, skriptu prostředí s samorozbalovacím binárním souborem. Soubor můžete spustit pomocí `sh` nebo přidat oprávnění ke spuštění do samotného souboru.

>[!NOTE]
> K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.
>

| Parametr | Popis |
|:--|:--|
| -Help | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| --check | Ověřte oprávnění a operační systém, ale agenta neinstalujte. |

Pokud například chcete spustit instalační program s `-help` parametrem, zadejte **InstallDependencyAgent-Linux64. bin-Help**.

Nainstalujte agenta závislostí pro Linux jako kořen spuštěním příkazu `sh InstallDependencyAgent-Linux64.bin` .

Pokud se nepovede spustit agenta závislostí, podrobnější informace o chybě najdete v protokolech. V agentech Linux se adresář protokolu */var/opt/Microsoft/Dependency-agent/log*.

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Příklady instalačního skriptu

Chcete-li snadno nasadit agenta závislostí na mnoho serverů najednou, je k dispozici následující ukázkový skript pro stažení a instalaci agenta závislostí v systému Windows nebo Linux.

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

## <a name="desired-state-configuration"></a>Desired State Configuration

Chcete-li nasadit agenta závislostí pomocí konfigurace požadovaného stavu (DSC), můžete použít modul xPSDesiredStateConfiguration s následujícím příkladem kódu:

```powershell
configuration VMInsights {

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

Pokud se pracovní prostor Log Analytics, na který řešení odkazuje, už není nakonfigurovaný tak, aby shromáždil čítače výkonu vyžadované řešením, musíte je povolit. Můžete to udělat jedním ze dvou způsobů:
* Ručně, jak je popsáno v tématu [zdroje dat o výkonu Windows a Linux v Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a spuštěním skriptu PowerShellu, který je dostupný z [galerie Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Nasazení Azure Monitor pro virtuální počítače

Tato metoda zahrnuje šablonu JSON, která určuje konfiguraci pro povolení součástí řešení ve vašem pracovním prostoru Log Analytics.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si téma:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud chcete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.27 nebo novější. Pro identifikaci vaší verze spusťte `az --version` . Pokud chcete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Vytvoření a spuštění šablony

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Uložte tento soubor jako *installsolutionsforvminsights.js* do místní složky.

1. Zachyťte hodnoty pro pole *pracovní prostor*, *ResourceGroupName*a *WorkspaceLocation*. Hodnota pro název *pracovního prostoru* je název vašeho pracovního prostoru Log Analytics. Hodnota pro *WorkspaceLocation* je oblast, ve které je pracovní prostor definován.

1. Tuto šablonu jste připraveni nasadit pomocí následujícího příkazu PowerShellu:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu, která zahrnuje výsledek:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po povolení monitorování může trvat asi 10 minut, než budete moct zobrazit stav a metriky pro hybridní počítač.

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="vm-doesnt-appear-on-the-map"></a>Virtuální počítač se na mapě nezobrazí.

Pokud se instalace agenta závislostí zdařila, ale váš počítač se na mapě nezobrazuje, Diagnostikujte problém pomocí následujících kroků.

1. Je Dependency Agent správně nainstalovaný? Můžete to ověřit tak, že zkontrolujete, jestli je příslušná služba nainstalovaná a spuštěná.

    **Windows**: vyhledejte službu s názvem Microsoft Dependency agent.

    **Linux**: vyhledejte běžící proces Microsoft-Dependency-agent.

2. Jste na [cenové úrovni bezplatné Log Analytics](./solutions.md)? Bezplatný plán umožňuje až pět jedinečných počítačů. Žádné následné počítače se na mapě nezobrazí, i když už předchozí pět neposílá data.

3. Odesílá počítač data protokolu a výkonu do Azure Monitor protokolů? Pro váš počítač proveďte následující dotaz:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Vrátil (a) jeden nebo více výsledků? Jsou data aktuální? Pokud ano, Váš agent Log Analytics správně funguje a komunikuje se službou. Pokud ne, ověřte agenta na serveru: [Log Analytics agenta pro řešení potíží s Windows](../platform/agent-windows-troubleshoot.md) nebo [agenta Log Analytics pro řešení potíží](../platform/agent-linux-troubleshoot.md)se systémem Linux.

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Počítač se zobrazí na mapě, ale nemá žádné procesy.

Pokud na mapě vidíte Server, ale nemá žádná data o procesu nebo připojení, která indikuje, že je agent závislostí nainstalovaný a spuštěný, ale ovladač jádra se nenačetl.

Zkontrolujte soubor C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) nebo soubor /var/opt/microsoft/dependency-agent/log/service.log (Linux). Poslední řádky souboru by měly obsahovat informace o tom, proč se jádro nenačetlo. Například pokud jste jádro aktualizovali, nemusí být podporované v Linuxu.


## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace k dispozici pro analýzu pomocí Azure Monitor pro virtuální počítače.

- Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md).

- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).
