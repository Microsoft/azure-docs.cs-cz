---
title: Nasazení pracovní prostor Studio pomocí Azure Resource Manageru
titleSuffix: Azure Machine Learning Studio
description: Postup nasazení pracovního prostoru pro Azure Machine Learning Studio pomocí šablony Azure Resource Manageru
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 91413aa461261824782717ae4edacc2757ad5405
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791130"
---
# <a name="deploy-azure-machine-learning-studio-workspace-using-azure-resource-manager"></a>Nasazení aplikace Azure Machine Learning Studio prostoru pomocí Azure Resource Manageru

Pomocí Azure Resource Manageru šablony nasazení šetří čas vyznačuje mimořádně škálovatelný způsob, jak nasadit vzájemně propojených součástí ověřování a mechanismus opakování. Nastavit pracovní prostory Azure Machine Learning Studio, například musíte nejprve nakonfigurovat účet úložiště Azure a pak nasaďte váš pracovní prostor. Představte si to ručně pro stovky pracovní prostory. Jednodušší alternativu je použití šablony Azure Resource Manageru k nasazení pracovnímu prostoru Studio a všechny jeho závislosti. Tento článek vás provede krok za krokem tohoto procesu. Skvělý přehled Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Podrobný postup: vytvořit pracovní prostor Machine Learning
Jsme se vytvořit skupinu prostředků Azure a pak nasaďte nový účet úložiště Azure a nový Azure Studio pracovní prostor Machine Learning pomocí šablony Resource Manageru. Po dokončení nasazení můžeme se vytiskne důležité informace o pracovních prostorech, které byly vytvořeny (primární klíč, ID pracovního prostoru a adresu URL do pracovního prostoru).

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

Ověřte, že skupina prostředků je správně zřízený. **Stav zřizování** by měl být "byla úspěšná."
Název skupiny prostředků pomocí šablony slouží ke generování názvu účtu úložiště. Název účtu úložiště musí být dlouhý 3 až 24 znaků a použít pouze číslice a malá písmena.

![Skupina prostředků](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Pomocí nasazení skupiny prostředků, nasaďte nový pracovní prostor Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po dokončení nasazení je jednoduché pro přístup k vlastnostem pracovního prostoru, který jste nasadili. Například můžete přistupovat primární klíč tokenu.

```powershell
# Access Azure Machine Learning studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Dalším způsobem, jak získat tokeny existující pracovní prostor je použití příkazu Invoke-AzResourceAction. Například můžete vytvořit seznam primární a sekundární tokeny všechny pracovní prostory.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po zřízení pracovního prostoru můžete automatizovat řadu úloh Azure Machine Learning Studio pomocí [modul PowerShell pro Azure Machine Learning Studio](https://aka.ms/amlps).

## <a name="next-steps"></a>Další postup

* Další informace o [Tvorba šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).
* Podívejte se na [úložišti šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
* Podívejte se na video o [Azure Resource Manageru](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zobrazit [nápovědy referenční dokumentace šablony Resource Manageru](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
