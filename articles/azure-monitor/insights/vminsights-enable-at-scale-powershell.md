---
title: Povolit monitorování Azure pro virtuální počítače (preview) pomocí šablon Resource Manageru nebo Azure Powershellu | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro jeden nebo více Azure virtual machines nebo virtuálního počítače škálovací sady pomocí Azure Powershellu nebo šablony Azure Resource Manageru.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144250"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Povolit monitorování Azure pro virtuální počítače (preview) pomocí šablon Resource Manageru nebo Azure Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (preview) pro virtuální počítače Azure nebo škálovacích sad virtuálních počítačů pomocí šablon Azure Resource Manageru nebo Azure Powershellu. Na konci tohoto procesu se úspěšně začali jste sledování všech vašich virtuálních počítačů a další, pokud některý dochází výkonem a dostupností způsobené.

## <a name="set-up-a-log-analytics-workspace"></a>Nastavte pracovní prostor Log Analytics 

Pokud nemáte pracovní prostor Log Analytics, musíte ho vytvořit. Kontrola metody, které jsou navržené v [požadavky](vminsights-enable-overview.md#log-analytics) části předtím, než budete pokračovat s pokyny k jeho konfiguraci. Poté mohou být dokončeny nasazení služby Azure Monitor pro virtuální počítače pomocí metody šablony Azure Resource Manageru.

### <a name="enable-performance-counters"></a>Povolit čítače výkonu

Pokud pracovní prostor Log Analytics, který je odkazován řešení ještě nenakonfigurovala získat čítače výkonu, vyžadují řešení, musíte je povolit. To lze provést jedním ze dvou způsobů:
* Ručně, jak je popsáno v [Windows a Linuxem zdroje dat výkonu do Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Stažením a instalací, který je k dispozici skript prostředí PowerShell [Galerie prostředí PowerShell pro Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Nainstalujte řešení ServiceMap a InfrastructureInsights
Tato metoda zahrnuje šablony JSON, který určuje konfiguraci pro povolení součásti řešení ve vašem pracovním prostoru Log Analytics.

Pokud si nejste jisti, jak nasadit prostředky pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud chcete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pro instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Uložte soubor jako *installsolutionsforvminsights.json* do místní složky.

1. Zachycení hodnoty *WorkspaceName*, *ResourceGroupName*, a *WorkspaceLocation*. Hodnota pro *WorkspaceName* je název pracovního prostoru Log Analytics. Hodnota pro *WorkspaceLocation* je pracovní prostor je definována v oblasti.

1. Jste připraveni k nasazení této šablony.
 
    * Do složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Změna konfigurace může trvat několik minut déle. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Chcete-li pomocí Azure CLI, spusťte následující příkaz:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Změna konfigurace může trvat několik minut déle. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Povolení s využitím šablon Azure Resource Manageru
Příklad šablony Azure Resource Manageru pro registraci jsme vytvořili virtuální počítače a škálovací sady virtuálních počítačů. Tyto šablony zahrnují scénáře, které slouží k povolení monitorování na existující prostředek a vytvořte nový prostředek, který má povoleno monitorování.

>[!NOTE]
>Šablona musí být nasazený ve stejné skupině prostředků jako prostředek by se měly na palubě.

Pokud si nejste jisti, jak nasadit prostředky pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud chcete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pro instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Stáhněte si šablony

Šablony Azure Resource Manageru jsou k dispozici v souboru archivu (.zip), který můžete [Stáhnout](https://aka.ms/VmInsightsARMTemplates) z našeho úložiště GitHub. Obsah souboru zahrnout složky, které představují jednotlivých scénářích nasazení se souborem šablonu a parametry. Předtím, než ji spustit, upravit soubor parametrů a zadejte požadované hodnoty. Neupravujte soubor šablony, pokud je potřeba ji upravit, aby vaše konkrétní požadavky na podporu. Po úpravě souboru parametrů, můžete ji nasadit pomocí následujících metod popsaných dále v tomto článku. 

Soubor ke stažení obsahuje následující šablony pro různé scénáře:

- **ExistingVmOnboarding** šablony umožňuje Azure Monitor pro virtuální počítače, pokud virtuální počítač už existuje.
- **NewVmOnboarding** šablona vytvoří virtuální počítač a umožňuje monitorování Azure pro virtuální počítače ho chcete sledovat.
- **ExistingVmssOnboarding** šablony umožňuje Azure Monitor pro virtuální počítače, pokud virtuální počítač škálovací sady, již existuje.
- **NewVmssOnboarding** šablona vytváří škálovací sady virtuálních počítačů a umožňuje monitorování Azure pro virtuální počítače na jejich monitorování.
- **ConfigureWorksapce** šablony nakonfiguruje pracovního prostoru Log Analytics k podpoře monitorování Azure pro virtuální počítače tím, že na řešení a shromažďování čítačů výkonu operačního systému Linux a Windows.

>[!NOTE]
>Pokud již byly k dispozici škálovací sady virtuálních počítačů a zásad upgradu je nastavená na **ruční**, nebude aktivováno monitorování Azure pro virtuální počítače pro instance ve výchozím nastavení po spuštění **ExistingVmssOnboarding** Šablona Azure Resource Manageru. Je nutné ručně upgradovat instance.

### <a name="deploy-by-using-azure-powershell"></a>Nasazení s využitím Azure PowerShellu

Následující krok umožňuje monitorování pomocí Azure Powershellu.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Změna konfigurace může trvat několik minut déle. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

Následující krok umožňuje monitorování pomocí Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Výstup vypadá přibližně takto:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Povolit pomocí Powershellu

Chcete-li povolit monitorování Azure pro virtuální počítače pro více virtuálních počítačů nebo škálovacích sad virtuálních počítačů, použijte skript Powershellu [instalace VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Je k dispozici z Galerie prostředí PowerShell Azure. Tento skript projde:

- Každý virtuální počítač a virtuální počítače škálovací sady v rámci vašeho předplatného.
- Skupinu prostředků s vymezeným oborem, která je zadána *ResourceGroup*. 
- Jeden virtuální počítač nebo virtuální počítač škálovací sadu, která je určená *název*.

Pro každý virtuální počítač nebo virtuální počítač ve škálovací sadě skript ověří, zda je rozšíření virtuálního počítače již nainstalován. Pokud není nainstalované rozšíření virtuálního počítače, skript se pokusí znovu nainstalovat. Pokud je nainstalované rozšíření virtuálního počítače, skript nainstaluje rozšíření virtuálních počítačů agenta Log Analytics a závislostí.

Tento skript vyžaduje modul Azure PowerShell Az verze 1.0.0 nebo novějším. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte také spustit `Connect-AzAccount` vytvořit připojení k Azure.

Pokud chcete získat seznam argumentů podrobnosti a příklady použití skriptu, spusťte `Get-Help`.

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

## <a name="next-steps"></a>Další postup

Teď, když je zapnuté monitorování pro vaše virtuální počítače, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače.
 
- Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). 
- Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). 
- Identifikujte kritické body a celkové využití s výkonem Virtuálního počítače, naleznete v tématu [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md). 
- Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).