---
title: Povolení Azure Monitoru pro virtuální počítače pomocí PowerShellu nebo šablon
description: Tento článek popisuje, jak povolíte Azure Monitor pro virtuální počítače pro jeden nebo více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů pomocí azure powershellu nebo šablony Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480840"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Povolení Azure Monitoru pro virtuální počítače pomocí azure prostředí Nebo šablon Správce prostředků

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítače Azure nebo škálovací sady virtuálních počítačů pomocí azure powershellu nebo šablony Azure Resource Manager. Na konci tohoto procesu úspěšně začnete sledovat všechny vaše virtuální počítače a zjistit, zda se některé mají problémy s výkonem nebo dostupností.

## <a name="set-up-a-log-analytics-workspace"></a>Nastavení pracovního prostoru Analýzy protokolů

Pokud nemáte pracovní prostor Log Analytics, musíte ho vytvořit. Zkontrolujte metody, které jsou navrženy v části [Požadavky,](vminsights-enable-overview.md#log-analytics) než budete pokračovat v krocích k jeho konfiguraci. Pak můžete dokončit nasazení Azure Monitor pro virtuální počítače pomocí metody šablony Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>Instalace řešení VMInsights

Tato metoda zahrnuje šablonu JSON, která určuje konfiguraci pro povolení součástí řešení v pracovním prostoru Log Analytics.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Chcete-li použít příkazové příkazové příkazové příkazy Azure, musíte nejprve nainstalovat a použít příkazový příkaz cli místně. Musíte spouštět Azure CLI verze 2.0.27 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud chcete nainstalovat nebo upgradovat azure cli, najdete [v tématu instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Uložte tento soubor jako *installsolutionsforvminsights.json* do místní složky.

1. Zachyťte hodnoty pro *název_pracovního_ ka*, *Název_prostředku_* *WorkspaceLocation* Hodnota pro *název pracovního_ kva to* je název pracovního prostoru Analýzy protokolů. Hodnota pro *WorkspaceLocation* je oblast, ve které je pracovní prostor definován.

1. Jste připraveni k nasazení této šablony.

    * Ve složce, která šablonu obsahuje, použijte následující příkazy Prostředí PowerShell:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu a obsahuje výsledek:

        ```output
        provisioningState       : Succeeded
        ```

    * Spuštění následujícího příkazu pomocí příkazového příkazu Azure:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující a obsahuje výsledek:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Povolení pomocí šablon Azure Resource Manageru

Vytvořili jsme příklady šablon Azure Resource Manager pro připojení virtuálních počítačů a škálovacísady virtuálních počítačů. Tyto šablony zahrnují scénáře, které můžete použít k povolení monitorování existujícího prostředku a k vytvoření nového prostředku, který má monitorování povoleno.

>[!NOTE]
>Šablona musí být nasazena ve stejné skupině prostředků jako prostředek, který má být uveden na palubu.

Pokud nevíte, jak nasadit prostředky pomocí šablony, přečtěte si:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Chcete-li použít příkazové příkazové příkazové příkazy Azure, musíte nejprve nainstalovat a použít příkazový příkaz cli místně. Musíte spouštět Azure CLI verze 2.0.27 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud chcete nainstalovat nebo upgradovat azure cli, najdete [v tématu instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Stáhnout šablony

Šablony Azure Resource Manager jsou k dispozici v archivním souboru (.zip), který si můžete [stáhnout](https://aka.ms/VmInsightsARMTemplates) z našeho úložiště GitHub. Obsah souboru obsahuje složky, které představují každý scénář nasazení se šablonou a souborem parametrů. Před jejich spuštěním upravte soubor parametrů a zadejte požadované hodnoty. Neupravujte soubor šablony, pokud jej nepotřebujete přizpůsobit tak, aby podporoval vaše konkrétní požadavky. Po úpravě souboru parametrů jej můžete nasadit pomocí následujících metod popsaných dále v tomto článku.

Soubor ke stažení obsahuje následující šablony pro různé scénáře:

- **Existující Šablona VmOnboarding** umožňuje Azure Monitor pro virtuální počítače, pokud virtuální počítač už existuje.
- **Šablona NewVmOnboarding** vytvoří virtuální počítač a umožní Azure Monitoru pro virtuální počítače, aby ho monitorovali.
- **Šablona ExistingVmssOnboarding** umožňuje Azure Monitor pro virtuální počítače, pokud škálovací sada virtuálních strojů už existuje.
- **Šablona NewVmssOnboarding** vytváří škálovací sady virtuálních strojů a umožňuje Azure Monitoru pro virtuální počítače je monitorovat.
- **Šablona ConfigureWorkspace** konfiguruje pracovní prostor Log Analytics tak, aby podporoval Azure Monitor pro virtuální počítače povolením řešení a kolekce čítačů výkonu operačního systému Linux a Windows.

>[!NOTE]
>Pokud škálovací sady virtuálních strojů už byly k dispozici a zásady upgradu jsou **nastavené**na ruční , Azure Monitor pro virtuální počítače se po spuštění šablony **ExistingVmssOnboarding** Azure Resource Manager ve výchozím nastavení neaktivuje. Je nutné ručně upgradovat instance.

### <a name="deploy-by-using-azure-powershell"></a>Nasazení s využitím Azure PowerShellu

Následující krok umožňuje monitorování pomocí Azure PowerShellu.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následujícímu a obsahuje výsledek:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Nasazení pomocí azure cli

Následující krok umožňuje monitorování pomocí azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Výstup se podobá následující:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Povolení pomocí PowerShellu

Pokud chcete povolit Azure Monitor pro virtuální počítače pro více virtuálních počítačů nebo škálovací sady virtuálních strojů, použijte skript PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Je k dispozici v Galerii Azure PowerShell. Tento skript iterates prostřednictvím:

- Každý virtuální počítač a škálovací sada virtuálních strojů ve vašem předplatném.
- Skupina prostředků s vymezeným oborem určená skupinou *ResourceGroup*.
- Jeden virtuální počítač nebo škálovací sada virtuálního počítače určená *názvem*.

Pro každý virtuální počítač nebo škálovací sadu virtuálních počítačů skript ověří, jestli je rozšíření virtuálního počítače už nainstalované. Pokud je nainstalováno rozšíření virtuálního zařízení, skript se pokusí přeinstalovat. Pokud není nainstalované rozšíření virtuálního zařízení, skript nainstaluje rozšíření virtuálních aplikací Pro analýzu protokolů a závislostí.

Ověřte, zda používáte modul Azure PowerShell Az `Enable-AzureRM` verze 1.0.0 nebo novější s povolenými aliasy kompatibility. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte taky `Connect-AzAccount` spustit k vytvoření připojení s Azure.

Chcete-li získat seznam podrobností argumentu skriptu `Get-Help`a ukázkového použití, spusťte .

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

Následující příklad ukazuje použití příkazů Prostředí PowerShell ve složce k povolení Azure Monitor pro virtuální počítače a pochopení očekávaného výstupu:

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

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace dostupné pro analýzu pomocí Azure Monitoru pro virtuální počítače.

- Informace o zjištěných závislostech aplikací najdete [v tématu Zobrazení programu Azure Monitor for VMS .](vminsights-maps.md)

- Informace o problémových místech a celkovém využití s výkonem virtuálního počítače najdete v [tématu Zobrazení výkonu virtuálních počítačích Azure](vminsights-performance.md).
