---
title: Vytvoření Azure Machine Learning experimentování pomocí šablony Azure Resource Manager | Microsoft Docs
description: Tento článek poskytuje příklad vytvoření účtu Azure Machine Learning experimentování pomocí šablony Azure Resource Manager.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 65efee0eea5e2595ef8833788281c5ba60ca3a43
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830778"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Konfigurovat službu Azure Machine Learning experimentování

## <a name="overview"></a>Přehled
Účet Azure Služba Machine Learning experimentování, pracovní prostor a projekt jsou prostředky Azure. Jako takový je lze nasadit pomocí šablony správce prostředků. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Nasazení šablony
Nasazení šablony vyžaduje pouze několik kroků v Azure rozhraní příkazového řádku nebo na portálu Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Nasazení šablony z příkazového řádku
Pomocí rozhraní příkazového řádku, jeden příkaz můžete nasadit šablonu do existující skupiny prostředků.
Viz následující informace o vytváření šablony.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Nasazení šablony z portálu Azure
Pokud dáváte přednost, můžete také použít portál Azure k vytvoření a nasazení šablony. Postupujte následovně:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** a vyhledejte "šablony."
3. Vyberte **šablony**.
4. Klikněte na **+ přidat** a zkopírujte informace o vaší šablony. 
5. Vyberte šablonu, vytvořili v kroku #4 a klikněte na tlačítko **nasadit**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Vytvořit šablonu z existujících prostředků Azure na portálu Azure
Pokud již máte počítači Azure experimentování účet k dispozici v [portál Azure](https://portal.azure.com), můžete z prostředku vygenerovat šablonu. 

1. Přejděte k účtu Azure experimenty v [portál Azure](https://portal.azure.com).
2. V části **nastavení**, klikněte na **skriptu pro automatizaci**.
3. Stáhněte šablony. 

Alternativně můžete ručně upravit soubory šablon. Viz následující příklad parametrů šablony a souborů. 

### <a name="template-file-example"></a>Příklad souboru šablony
Vytvořte soubor s názvem "šablony file.json" s níže obsah. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Parametry 
Vytvořte soubor s níže obsah a uložte ho jako < Parameters.JSON tímto kódem >. 

Existují tři hodnoty, které je možné změnit. 
* Název účtu: Název účtu experimenty.
* Umístění: Jeden z podporovaných oblastí Azure.
* Účet úložiště SKU: Azure ML se podporuje jenom standardní úložiště, není premium. Další informace o ukládání najdete v tématu [úložiště ÚVOD](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Další postup
* [Vytvořit a nainstalovat Azure Machine Learning](../service/quickstart-installation.md)
