---
title: 'ML Studio (Classic): nasazení pracovních prostorů pomocí Azure Resource Manager – Azure'
description: Postup nasazení pracovního prostoru pro Azure Machine Learning Studio (Classic) pomocí šablony Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520030"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Nasazení pracovního prostoru Azure Machine Learning Studio (klasický) pomocí Azure Resource Manager

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Použití šablony nasazení Azure Resource Manager vám ušetří čas tím, že vám poskytne škálovatelný způsob, jak nasadit propojené komponenty pomocí mechanismu ověřování a opakování. Chcete-li nastavit pracovní prostory Azure Machine Learning Studio (klasické), například je třeba nejprve nakonfigurovat účet služby Azure Storage a potom tento pracovní prostor nasadit. Představte si to ručně pro stovky pracovních prostorů. Jednodušší alternativou je použití šablony Azure Resource Manager k nasazení pracovního prostoru studia (Classic) a všech jeho závislostí. Tento článek vás provede jednotlivými kroky tohoto postupu. Skvělé přehledy Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Podrobný postup: vytvoření pracovní prostor Machine Learning
Vytvoříme skupinu prostředků Azure a pak nasadíme nový účet Azure Storage a nový pracovní prostor Azure Machine Learning Studio (Classic) pomocí šablony Správce prostředků. Po dokončení nasazení vyhledáme důležité informace o pracovních prostorech, které byly vytvořeny (primární klíč, ID pracovního prostoru a adresa URL pracovního prostoru).

### <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manager

Pracovní prostor Machine Learning vyžaduje účet služby Azure Storage, který ukládá datovou sadu, která je k ní propojená.
Následující šablona používá název skupiny prostředků pro vygenerování názvu účtu úložiště a názvu pracovního prostoru.  Při vytváření pracovního prostoru se jako vlastnost používá taky název účtu úložiště.

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
Uložit tuto šablonu jako mlworkspace.jsdo souboru pod c:\Temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Nasazení skupiny prostředků na základě šablony

* Otevření Powershellu
* Instalace modulů pro Azure Resource Manager a správu služeb Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Tyto kroky stahují a instalují moduly nezbytné k dokončení zbývajících kroků. Tento postup je potřeba provést jenom jednou v prostředí, ve kterém spouštíte příkazy PowerShellu.

* Ověřování v Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Tento krok je potřeba opakovat pro každou relaci. Po ověření by se měly zobrazit informace o vašem předplatném.

![Účet Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Teď, když máme přístup k Azure, můžeme vytvořit skupinu prostředků.

* Vytvoření skupiny prostředků

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ověřte, zda je skupina prostředků správně zřízena. **ProvisioningState** by měla být "úspěšná".
Název skupiny prostředků se používá v šabloně k vygenerování názvu účtu úložiště. Název účtu úložiště musí být dlouhý 3 až 24 znaků a obsahovat jenom číslice a malá písmena.

![Skupina prostředků](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Pomocí nasazení skupiny prostředků nasaďte nový pracovní prostor Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po dokončení nasazení je jednoduché získat přístup k vlastnostem pracovního prostoru, který jste nasadili. Můžete například získat přístup k tokenu primárního klíče.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Dalším způsobem, jak načíst tokeny stávajícího pracovního prostoru, je použít příkaz Invoke-AzResourceAction. Můžete například zobrazit seznam primárních a sekundárních tokenů všech pracovních prostorů.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po zřízení pracovního prostoru můžete automatizovat řadu Azure Machine Learning Studioch (klasických) úloh pomocí [modulu PowerShellu pro Azure Machine Learning Studio (Classic)](https://aka.ms/amlps).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [vytváření šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Podívejte se na [úložiště šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates).
* Podívejte se na toto video o [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Viz Referenční informace k [šabloně správce prostředků](/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->