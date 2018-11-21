---
title: Nasazení pracovního prostoru Machine Learning pomocí Azure Resource Manageru | Dokumentace Microsoftu
description: Postup nasazení pracovního prostoru pro Azure Machine Learning pomocí šablony Azure Resource Manageru
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/05/2018
ms.openlocfilehash: e02dac0543fc7c59ae7b7d29bab1dfedb8bf737f
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263172"
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Nasazení pracovního prostoru Machine Learning pomocí Azure Resource Manageru
## <a name="introduction"></a>Úvod
Pomocí Azure Resource Manageru šablony nasazení šetří čas vyznačuje mimořádně škálovatelný způsob, jak nasadit vzájemně propojených součástí ověřování a mechanismus opakování. Nastavení pracovních prostorů Azure Machine Learning, například musíte nejprve nakonfigurovat účet úložiště Azure a pak nasaďte váš pracovní prostor. Představte si to ručně pro stovky pracovní prostory. Jednodušší alternativu je použití šablony Azure Resource Manageru k nasazení pracovního prostoru služby Azure Machine Learning a všechny jeho závislosti. Tento článek vás provede krok za krokem tohoto procesu. Skvělý přehled Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Podrobný postup: vytvořit pracovní prostor Machine Learning
Jsme se vytvořit skupinu prostředků Azure a pak nasaďte nový účet úložiště Azure a nový Azure pracovní prostor Machine Learning pomocí šablony Resource Manageru. Po dokončení nasazení můžeme se vytiskne důležité informace o pracovních prostorech, které byly vytvořeny (primární klíč, ID pracovního prostoru a adresu URL do pracovního prostoru).

### <a name="create-an-azure-resource-manager-template"></a>Vytvoření šablony Azure Resource Manageru
Pracovní prostor Machine Learning vyžaduje účet Azure storage k uložení datové sady na sebe.
Následující šablony používá název skupiny prostředků pro vygenerování názvu účtu úložiště a název pracovního prostoru.  Také používá název účtu úložiště jako vlastnost při vytváření pracovního prostoru.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Tyto kroky stáhněte a nainstalujte moduly, které jsou nezbytné pro dokončení zbývajících kroků. To je pouze potřeba provést v prostředí, ve kterém spouštíte příkazy prostředí PowerShell.   

* Ověřování v Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Tento krok je potřeba opakovat pro každou relaci. Po ověření, má být zobrazena informace o vašem předplatném.

![Účet Azure][1]

Když teď máme přístup k Azure, můžeme vytvořit skupinu prostředků.

* Vytvoření skupiny prostředků

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ověřte, že skupina prostředků je správně zřízený. **Stav zřizování** by měl být "byla úspěšná."
Název skupiny prostředků pomocí šablony slouží ke generování názvu účtu úložiště. Název účtu úložiště musí být dlouhý 3 až 24 znaků a použít pouze číslice a malá písmena.

![Skupina prostředků][2]

* Pomocí nasazení skupiny prostředků, nasaďte nový pracovní prostor Machine Learning.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po dokončení nasazení je jednoduché pro přístup k vlastnostem pracovního prostoru, který jste nasadili. Například můžete přistupovat primární klíč tokenu.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Dalším způsobem, jak získat tokeny existující pracovní prostor je použití příkazu Invoke-AzureRmResourceAction. Například můžete vytvořit seznam primární a sekundární tokeny všechny pracovní prostory.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Po zřízení pracovního prostoru můžete automatizovat řadu úloh Azure Machine Learning Studio pomocí [modul PowerShell pro Azure Machine Learning](https://aka.ms/amlps).

## <a name="next-steps"></a>Další kroky
* Další informace o [Tvorba šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Podívejte se na [úložišti šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates). 
* Podívejte se na video o [Azure Resource Manageru](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
