---
title: Nasazení pracovního prostoru Studia (klasického) pomocí Správce prostředků Azure
titleSuffix: ML Studio (classic) - Azure
description: Jak nasadit pracovní prostor pro Azure Machine Learning Studio (klasické) pomocí šablony Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 34333d4fe6e9b34a0c8b56cca8123f4ed93a917a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218116"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Nasazení pracovního prostoru Azure Machine Learning Studio (klasické) pomocí Správce prostředků Azure

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Použití šablony nasazení Azure Resource Manager vám ušetří čas tím, že vám poskytne škálovatelný způsob nasazení propojených součástí s mechanismem ověřování a opakování. Chcete-li například nastavit pracovní prostory Azure Machine Learning Studio (klasické), musíte nejdřív nakonfigurovat účet úložiště Azure a pak nasadit pracovní prostor. Představte si, že to děláte ručně pro stovky pracovních prostorů. Jednodušší alternativou je použití šablony Azure Resource Manager k nasazení studio (klasické) pracovního prostoru a všech jeho závislostí. Tento článek vás provede tímto procesem krok za krokem. Skvělý přehled Správce prostředků Azure najdete v tématu [Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok za krokem: vytvoření pracovního prostoru strojového učení
Vytvoříme skupinu prostředků Azure a pak nasadíme nový účet úložiště Azure a nový pracovní prostor Azure Machine Learning Studio (klasický) pomocí šablony Správce prostředků. Po dokončení nasazení vytiskneme důležité informace o pracovních prostorech, které byly vytvořeny (primární klíč, ID pracovního prostoru a adresa URL pracovního prostoru).

### <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Pracovní prostor Machine Learning workspace vyžaduje účet úložiště Azure k ukládání datové sady s ním propojené.
Následující šablona používá název skupiny prostředků ke generování názvu účtu úložiště a názvu pracovního prostoru.  Používá také název účtu úložiště jako vlastnost při vytváření pracovního prostoru.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Tuto šablonu uložte jako soubor mlworkspace.json pod c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Nasazení skupiny prostředků na základě šablony

* Otevření Powershellu
* Instalace modulů pro Azure Resource Manager a Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Tyto kroky stáhnout a nainstalovat moduly potřebné k dokončení zbývajících kroků. To je třeba provést pouze jednou v prostředí, kde provádíte příkazy prostředí PowerShell.

* Ověření do Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Tento krok je třeba opakovat pro každou relaci. Po ověření by se měly zobrazit informace o předplatném.

![Účet Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Teď, když máme přístup k Azure, můžeme vytvořit skupinu prostředků.

* Vytvoření skupiny prostředků

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ověřte, zda je skupina prostředků správně zřízena. **ZřizováníState** by měl být "Úspěšné."
Název skupiny prostředků používá šablona ke generování názvu účtu úložiště. Název účtu úložiště musí mít délku 3 až 24 znaků a používat pouze čísla a malá písmena.

![Skupina prostředků](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Pomocí nasazení skupiny prostředků nasaďte nový pracovní prostor machine learningu.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po dokončení nasazení je snadné získat přístup k vlastnostem pracovního prostoru, který jste nasadili. Můžete například získat přístup k tokenu primárního klíče.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Dalším způsobem, jak načíst tokeny existujícího pracovního prostoru, je použití příkazu Invoke-AzResourceAction. Můžete například uvést primární a sekundární tokeny všech pracovních prostorů.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po zřízení pracovního prostoru můžete také automatizovat mnoho úloh Azure Machine Learning Studio (klasické) pomocí [modulu PowerShell pro Azure Machine Learning Studio (klasické).](https://aka.ms/amlps)

## <a name="next-steps"></a>Další kroky

* Další informace o [vytváření šablon Azure Resource Manageru](../../azure-resource-manager/templates/template-syntax.md).
* Podívejte se na [úložiště šablon Azure Quickstart .](https://github.com/Azure/azure-quickstart-templates)
* Podívejte se na toto video o [Správci prostředků Azure](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Viz [nápověda k odkazu na šablonu Správce prostředků](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
