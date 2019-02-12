---
title: Použití šablony k vytvoření pracovního prostoru
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvořit nový pracovní prostor služby Azure Machine Learning pomocí šablony Azure resource Manageru.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: haining
author: hning86
ms.date: 02/11/2019
ms.openlocfilehash: 71513c91d83f54359e43a41e178d91fe97a4ce82
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56101109"
---
# <a name="create-an-azure-machine-learning-service-workspace-by-using-a-template"></a>Vytvořit pracovní prostor služby Azure Machine Learning pomocí šablony

V tomto článku se dozvíte několik způsobů, jak vytvořit pracovní prostor služby Azure Machine Learning pomocí šablon Azure Resource Manageru. 

Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatného Azure**. Pokud nemáte, zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree).

* Použití šablony z rozhraní příkazového řádku, je nutné buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Šablona Resource Manageru

Šablony Resource Manageru umožňuje snadno vytvářet prostředky jako jediné koordinované operace. Šablona je dokument JSON s definicí prostředků, které jsou potřebné pro nasazení. Kromě toho může určovat parametrů nasazení. Parametry se používají při použití šablona zadávat vstupní hodnoty.

Následující šablony lze použít k vytvoření pracovního prostoru služby Azure Machine Learning service a související prostředky Azure:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Tato šablona vytvoří následující služby Azure:

* Skupina prostředků Azure
* Účet služby Azure Storage
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Pracovní prostor služby Azure Machine Learning

Skupina prostředků je kontejner, který obsahuje služby. Různé služby jsou vyžadované pracovního prostoru Azure Machine Learning.

Ukázková šablona má dva parametry:

* **Umístění** kde se vytvoří skupina prostředků a služeb.

    Šablona použije umístění, které jste vybrali pro většinu prostředků. Výjimkou je služba Application Insights, která není k dispozici ve všech umístěních, které jsou ostatním službám. Pokud vyberete umístění, kde není k dispozici, služba se vytvoří v umístění střed USA – jih.

* **Název pracovního prostoru**, což je popisný název pracovního prostoru Azure Machine Learning.

    Názvy jiných služeb, jsou generovány náhodně.

Další informace o šablonách najdete v následujících článcích:

* [Tvorba šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md)
* [Typy prostředků Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Postupujte podle kroků v [nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Při doručení na __úpravy šablony__ obrazovky, vložte do šablony z tohoto dokumentu.
1. Vyberte __Uložit__ použít šablonu. Zadejte následující informace a souhlas s uvedené podmínky a ujednání:

    * Předplatné: Vyberte předplatné Azure má použít pro tyto prostředky.
    * Skupina zdrojů: Vyberte nebo vytvořte skupinu prostředků k obsáhnutí služeb.
    * Název pracovního prostoru: Název, který se má použít pro pracovní prostor Azure Machine Learning, která bude vytvořena. Název pracovního prostoru musí být dlouhý 3 až 33 znaků. Může obsahovat jenom alfanumerické znaky a "-".
    * Umístění: Vyberte umístění, kde se prostředky vytvoří.

    ![Parametry šablony na webu Azure Portal](media/how-to-create-workspace-template/template-parameters.png)

Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Tento příklad předpokládá, že jste uložili šablony do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) a [nasazení privátní šablony Resource Manageru s tokenem SAS a prostředí Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

Tento příklad předpokládá, že jste uložili šablony do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) a [nasazení privátní šablony Resource Manageru s tokenem SAS a rozhraní příkazového řádku Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Další postup

* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní REST API Resource Manageru](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).