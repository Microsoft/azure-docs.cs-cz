---
title: Nasazení pracovní prostor Machine Learning s Azure Resource Manager | Microsoft Docs
description: Postup nasazení pracovního prostoru pro Azure Machine Learning pomocí šablony Azure Resource Manageru
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/05/2018
ms.openlocfilehash: 4ba75b1d1740486649cc8d4e012c3f780488cbe0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Nasazení pracovního prostoru Machine Learning pomocí Azure Resource Manageru
## <a name="introduction"></a>Úvod
Pomocí Azure Resource Manager šablonu nasazení šetří čas je tím, že jste škálovatelné způsob, jak nasadit vzájemně propojena k součástem s ověřování a opakujte mechanismus. Abyste nastavili Azure Machine Learning pracovní prostory, například musíte nejdřív nakonfigurovat účet úložiště Azure a pak nasadit pracovního prostoru. Představte si to ručně ve velkém množství pracovních prostorů. Jednodušší alternativou je pomocí šablony Azure Resource Manager k nasazení pracovní prostor služby Azure Machine Learning a všechny jeho závislé součásti. Tento článek vás provede krok za krokem tohoto procesu. Skvělý Přehled služby Správce prostředků Azure, najdete v části [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Postup: vytvoření pracovního prostoru Machine Learning
Jsme se vytvořit skupinu prostředků Azure a pak nasaďte nový účet úložiště Azure a nový Azure Machine Learning pracovní prostor pomocí šablony Resource Manageru. Po dokončení nasazení jsme se vytiskněte důležité informace o pracovních prostorech, které byly vytvořeny (primární klíč, ID pracovního prostoru a adresu URL do pracovního prostoru).

### <a name="create-an-azure-resource-manager-template"></a>Vytvořit šablonu Azure Resource Manager
Pracovní prostor Machine Learning vyžaduje účet úložiště Azure pro uložení datové sady na sebe napojenou.
Následující šablona používá název skupiny prostředků můžete vygenerovat název účtu úložiště a název pracovního prostoru.  Také používá název účtu úložiště jako vlastnost při vytváření pracovního prostoru.

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
Uložte jako soubor mlworkspace.json pod c:\temp\ této šablony.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Nasazení skupiny prostředků, na základě šablony
* Otevřete prostředí PowerShell
* Instalace modulů pro správu služby Azure a Azure Resource Manager  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Tyto kroky stáhnout a nainstalovat moduly, které jsou potřebné k dokončení zbývající kroky. Stačí, když to se provádí jednou v prostředí, kde jsou prováděny příkazy prostředí PowerShell.   

* Ověřování v Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Tento krok je potřeba zopakovat pro každou relaci. Po ověření, má být zobrazena informace o vašem předplatném.

![Účet Azure][1]

Teď, když máme přístup k Azure, můžeme vytvořit skupiny prostředků.

* Vytvoření skupiny prostředků

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ověřte, že je správně zřízený skupině prostředků. **Stav zřizování** by měl být "byla úspěšná."
Název skupiny prostředků se šablonou používá ke generování název účtu úložiště. Název účtu úložiště musí být v rozmezí 3 až 24 znaků a používat jenom číslice a malá písmena.

![Skupina prostředků][2]

* Pomocí nasazení skupiny prostředků, nasaďte nový pracovní prostor Machine Learning.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po dokončení nasazení je snadný přístup k vlastnosti pracovního prostoru, které jste nasadili. Například můžete přistupovat tokenu primární klíč.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Použití příkazu Invoke-AzureRmResourceAction je jiný způsob, jak načíst tokeny existujícímu pracovnímu prostoru. Například můžete vytvořit seznam tokenů primární a sekundární všech pracovních prostorů.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Po zřízení pracovním prostoru můžete také automatizovat celou řadu úloh Azure Machine Learning Studio pomocí [modul prostředí PowerShell pro Azure Machine Learning](http://aka.ms/amlps).

## <a name="next-steps"></a>Další kroky
* Další informace o [vytváření šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Podívejte se na [úložiště šablony Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates). 
* Přehrát toto video o [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
