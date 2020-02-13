---
title: Nasazení pracovního prostoru studia (Classic) pomocí Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Postup nasazení pracovního prostoru pro Azure Machine Learning Studio (Classic) pomocí šablony Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: f05ef9472f11a5025e9856cfb207cc0859f24c3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169354"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Nasazení pracovního prostoru Azure Machine Learning Studio (klasický) pomocí Azure Resource Manager

Pomocí Azure Resource Manageru šablony nasazení šetří čas vyznačuje mimořádně škálovatelný způsob, jak nasadit vzájemně propojených součástí ověřování a mechanismus opakování. Chcete-li nastavit pracovní prostory Azure Machine Learning Studio (klasické), například je třeba nejprve nakonfigurovat účet služby Azure Storage a potom tento pracovní prostor nasadit. Představte si to ručně pro stovky pracovní prostory. Jednodušší alternativou je použití šablony Azure Resource Manager k nasazení pracovního prostoru studia (Classic) a všech jeho závislostí. Tento článek vás provede krok za krokem tohoto procesu. Skvělé přehledy Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Podrobný postup: vytvořit pracovní prostor Machine Learning
Vytvoříme skupinu prostředků Azure a pak nasadíme nový účet Azure Storage a nový pracovní prostor Azure Machine Learning Studio (Classic) pomocí šablony Správce prostředků. Po dokončení nasazení můžeme se vytiskne důležité informace o pracovních prostorech, které byly vytvořeny (primární klíč, ID pracovního prostoru a adresu URL do pracovního prostoru).

### <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru

Pracovní prostor Machine Learning vyžaduje účet Azure storage k uložení datové sady na sebe.
Následující šablony používá název skupiny prostředků pro vygenerování názvu účtu úložiště a název pracovního prostoru.  Také používá název účtu úložiště jako vlastnost při vytváření pracovního prostoru.

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

### <a name="deploy-the-resource-group-based-on-the-template"></a>Nasazení skupiny prostředků, na základě šablony

* Otevření Powershellu
* Nainstalujte moduly Azure Resource Manageru a správy služeb prostředí Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Tyto kroky stáhněte a nainstalujte moduly, které jsou nezbytné pro dokončení zbývajících kroků. To je pouze potřeba provést v prostředí, ve kterém spouštíte příkazy prostředí PowerShell.

* Ověřování v Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Tento krok je potřeba opakovat pro každou relaci. Po ověření, má být zobrazena informace o vašem předplatném.

![Účet Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Když teď máme přístup k Azure, můžeme vytvořit skupinu prostředků.

* Vytvoření skupiny prostředků

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ověřte, že skupina prostředků je správně zřízený. **ProvisioningState** by měla být "úspěšná".
Název skupiny prostředků pomocí šablony slouží ke generování názvu účtu úložiště. Název účtu úložiště musí být dlouhý 3 až 24 znaků a použít pouze číslice a malá písmena.

![Skupina prostředků](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Pomocí nasazení skupiny prostředků, nasaďte nový pracovní prostor Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po dokončení nasazení je jednoduché pro přístup k vlastnostem pracovního prostoru, který jste nasadili. Například můžete přistupovat primární klíč tokenu.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Dalším způsobem, jak načíst tokeny stávajícího pracovního prostoru, je použít příkaz Invoke-AzResourceAction. Například můžete vytvořit seznam primární a sekundární tokeny všechny pracovní prostory.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po zřízení pracovního prostoru můžete automatizovat řadu Azure Machine Learning Studioch (klasických) úloh pomocí [modulu PowerShellu pro Azure Machine Learning Studio (Classic)](https://aka.ms/amlps).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [vytváření šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Podívejte se na [úložiště šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates).
* Podívejte se na toto video o [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Viz Referenční informace k [šabloně správce prostředků](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
