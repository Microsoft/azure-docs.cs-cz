---
title: Povolení Azure Monitor pro virtuální počítače (Classic) pomocí PowerShellu nebo šablon
description: Tento článek popisuje, jak povolíte Azure Monitor pro virtuální počítače pro jeden nebo několik virtuálních počítačů Azure nebo sady škálování virtuálních počítačů pomocí šablon Azure PowerShell nebo Azure Resource Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/14/2019
ms.openlocfilehash: 7d5107279654aa883a040a9168eb099a6543caa8
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286238"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Povolit Azure Monitor pro virtuální počítače (Preview) pomocí šablon Azure PowerShell nebo Správce prostředků

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (Preview) pro virtuální počítače Azure nebo sady škálování virtuálních počítačů pomocí šablon Azure PowerShell nebo Azure Resource Manager. Na konci tohoto procesu budete úspěšně započati monitorování všech virtuálních počítačů a zjistili jste, jestli u nich dochází k problémům s výkonem nebo dostupností.

## <a name="set-up-a-log-analytics-workspace"></a>Nastavte pracovní prostor Log Analytics 

Pokud nemáte pracovní prostor Log Analytics, budete ho muset vytvořit. Než budete pokračovat postupem konfigurace, přečtěte si metody, které jsou navržené v části [požadavky](vminsights-enable-overview.md#log-analytics) . Pak můžete dokončit nasazení Azure Monitor pro virtuální počítače pomocí metody Azure Resource Manager šablony.

### <a name="enable-performance-counters"></a>Povolit čítače výkonu

Pokud pracovní prostor Log Analytics, který je odkazován řešení ještě nenakonfigurovala získat čítače výkonu, vyžadují řešení, musíte je povolit. Můžete to udělat jedním ze dvou způsobů:
* Ručně, jak je popsáno v tématu [zdroje dat o výkonu Windows a Linux v Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a spuštěním skriptu PowerShellu, který je dostupný z [galerie Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-solution"></a>Instalace řešení ServiceMap

Tato metoda zahrnuje šablony JSON, který určuje konfiguraci pro povolení součásti řešení ve vašem pracovním prostoru Log Analytics.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si téma:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud chcete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.27 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud chcete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Uložte tento soubor jako *installsolutionsforvminsights. JSON* do místní složky.

1. Zachyťte hodnoty pro pole *pracovní prostor*, *ResourceGroupName*a *WorkspaceLocation*. Hodnota pro název *pracovního prostoru* je název vašeho pracovního prostoru Log Analytics. Hodnota pro *WorkspaceLocation* je oblast, ve které je pracovní prostor definován.

1. Jste připraveni k nasazení této šablony.
 
    * Do složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu, která zahrnuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Chcete-li pomocí Azure CLI, spusťte následující příkaz:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu příkladu, která obsahuje výsledek:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Povolit s Azure Resource Managermi šablonami

Vytvořili jsme příklady Azure Resource Manager šablon pro připojování virtuálních počítačů a sady škálování virtuálních počítačů. Tyto šablony obsahují scénáře, pomocí kterých můžete povolit monitorování stávajícího prostředku a vytvořit nový prostředek, který má povolené monitorování.

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků jako prostředek, který se má uvést na desce.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si téma:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud chcete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.27 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud chcete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Stažení šablon

Šablony Azure Resource Manager jsou k dispozici v souboru archivu (. zip), který si můžete [Stáhnout](https://aka.ms/VmInsightsARMTemplates) z našeho úložiště GitHub. Obsah souboru zahrnuje složky, které reprezentují jednotlivé scénáře nasazení, pomocí šablony a souboru parametrů. Než je spustíte, upravte soubor parametrů a zadejte požadované hodnoty. Neupravujte soubor šablony, pokud ho nepotřebujete přizpůsobit, aby podporoval vaše konkrétní požadavky. Po úpravě souboru parametrů ho můžete nasadit pomocí následujících metod popsaných dále v tomto článku. 

Soubor ke stažení obsahuje následující šablony pro různé scénáře:

- Šablona **ExistingVmOnboarding** povoluje Azure monitor pro virtuální počítače, pokud virtuální počítač už existuje.
- Šablona **NewVmOnboarding** vytvoří virtuální počítač a povolí Azure monitor pro virtuální počítače k jeho monitorování.
- Šablona **ExistingVmssOnboarding** povoluje Azure monitor pro virtuální počítače, pokud už existuje sada škálování virtuálního počítače.
- **NewVmssOnboarding** Template vytvoří virtuální počítač Scale Sets a povolí Azure monitor pro virtuální počítače k jejich monitorování.
- Šablona **ConfigureWorkspace** konfiguruje pracovní prostor Log Analytics tak, aby podporoval Azure monitor pro virtuální počítače tím, že umožňuje řešení a shromažďování čítačů výkonu operačního systému Linux a Windows.

>[!NOTE]
>Pokud se sada škálování virtuálních počítačů už nastavila a zásada upgradu je nastavená na **Ruční**, Azure monitor pro virtuální počítače po spuštění šablony Azure Resource Manager **ExistingVmssOnboarding** nebude ve výchozím nastavení povolená. Je nutné ručně upgradovat instance.

### <a name="deploy-by-using-azure-powershell"></a>Nasazení s využitím Azure PowerShellu

Následující krok umožňuje monitorování pomocí Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu, která zahrnuje výsledek:

```powershell
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Nasazení pomocí Azure CLI

Následující krok umožňuje monitorování pomocí Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Výstup se podobá následujícímu:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Povolit pomocí Powershellu

Pokud chcete povolit Azure Monitor pro virtuální počítače pro víc virtuálních počítačů nebo Virtual Machine Scale Sets, použijte skript PowerShellu [install-VMInsights. ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Je k dispozici z Galerie Azure PowerShell. Tento skript prochází:

- Všechny virtuální počítače a sady škálování virtuálních počítačů ve vašem předplatném.
- Rozsah skupiny prostředků, který je *určený skupinou zdrojů.* 
- Jeden virtuální počítač nebo sada škálování virtuálního počítače určené *názvem*.

Pro každý virtuální počítač nebo virtuální počítač ve škálovací sadě skript ověří, zda je rozšíření virtuálního počítače již nainstalován. Pokud je rozšíření virtuálního počítače nainstalované, skript se ho pokusí znovu nainstalovat. Pokud rozšíření virtuálního počítače není nainstalované, skript nainstaluje rozšíření virtuálního počítače agenta Log Analytics a závislostí.

Ověřte, že používáte Azure PowerShell modul AZ verze 1.0.0 nebo novější s povolenými `Enable-AzureRM`mi aliasy kompatibility. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit `Connect-AzAccount` a vytvořit připojení k Azure.

Chcete-li získat seznam podrobností o argumentu skriptu a příklady použití, spusťte `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

Následující příklad ukazuje pomocí příkazů Powershellu ve složce povolit monitorování Azure pro virtuální počítače a pochopení očekávaný výstup:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace k dispozici pro analýzu pomocí Azure Monitor pro virtuální počítače.
 
- Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md). 

- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md). 
