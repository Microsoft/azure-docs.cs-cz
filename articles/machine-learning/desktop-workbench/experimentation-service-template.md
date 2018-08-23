---
title: Vytvoření experimentování ve službě Azure Machine Learning pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Tento článek obsahuje příklad k vytvoření účtu služby experimentování ve službě Azure Machine Learning pomocí šablony Azure Resource Manageru.
services: machine-learning
author: hning86
ms.author: haining
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 7938eaa0e06c9a33034a7388d02845d60967774e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42060556"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Konfigurace služby experimentování ve službě Azure Machine Learning

## <a name="overview"></a>Přehled
Účet služby experimentování ve službě Machine Learning Azure, pracovní prostor a projektu jsou prostředky Azure. V důsledku toho bylo možné nasadit pomocí šablon správce prostředků. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Nasazení šablony
Nasazení šablony se vyžaduje jenom pár kroků v Azure rozhraní příkazového řádku nebo na webu Azure Portal.

### <a name="deploy-a-template-from-the-command-line"></a>Nasazení šablony z příkazového řádku
Pomocí rozhraní příkazového řádku, může jeden příkaz šablonu můžete nasadit do existující skupiny prostředků.
Zobrazí tyto informace o vytváření šablony.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Nasazení šablony z portálu Azure portal
Pokud dáváte přednost, můžete také použít webu Azure portal k vytvoření a nasazení šablony. Postupujte následovně:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** a vyhledejte položku "šablony."
3. Vyberte **šablony**.
4. Klikněte na **+ přidat** a zkopírujte údaje šablony. 
5. Vyberte šablonu, vytvořili v kroku #4 a klikněte na tlačítko **nasadit**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Vytvoření šablony z existujících prostředků Azure na webu Azure Portal
Pokud už máte experimentování ve službě Azure Machine účet k dispozici v [webu Azure portal](https://portal.azure.com), můžete generovat šablony z tohoto prostředku. 

1. Přejděte na účet služby experimentování ve službě Azure v [webu Azure portal](https://portal.azure.com).
2. V části **nastavení**, klikněte na **automatizační skript**.
3. Stáhněte si šablonu. 

Případně můžete ručně upravit soubory šablon. Viz následující příklad šablonu a parametry soubory. 

### <a name="template-file-example"></a>Příklad souboru šablony
Vytvořte soubor s názvem "šablony file.json" s následující obsah. 

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
Vytvořte soubor s níže obsah a uložte ho jako < parameters.json >. 

Existují tři hodnoty, které můžete změnit. 
* Název účtu: Název účtu služby experimentování ve službě.
* Umístění: Jeden z podporovaných oblastech Azure.
* SKU účtu úložiště: Služba Azure ML podporuje pouze úložiště úrovně standard, ne úrovně premium. Další informace o službě storage najdete v tématu [úložiště ÚVOD](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

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
* [Vytvořte a nainstalujte Azure Machine Learning](../service/quickstart-installation.md)
