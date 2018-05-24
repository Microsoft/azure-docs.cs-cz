---
title: Šablona Azure Resource Manageru s rozšířením VS Code | Dokumentace Microsoftu
description: Při práci na šablonách Resource Manageru můžete použít rozšíření Nástroje Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: f05b0baee3f11f498976377c69c38b3118f3c922
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358655"
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>Použití rozšíření Visual Studio Code k vytvoření šablony Azure Resource Manageru
Tento článek ukazuje výhody instalace a používání rozšíření Nástroje Azure Resource Manageru v editoru Visual Studio Code. Šablony Resource Manageru můžete ve VS Code vytvářet i bez tohoto rozšíření, poskytuje však možnosti automatického dokončování, které vývoj šablon zjednodušují. Navrhuje funkce šablon, parametry a proměnné, které jsou v šabloně dostupné.

K dokončení tohoto článku potřebujete [Visual Studio Code](https://code.visualstudio.com/).

Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md).

## <a name="create-the-template"></a>Vytvoření šablony

Tento článek vychází ze šablony, kterou jste vytvořili v tématu [Vytvoření a nasazení první šablony Azure Resource Manageru](resource-manager-create-first-template.md). Pokud tuto šablonu už máte, můžete tuto část přeskočit.

1. Pokud potřebujete šablonu vytvořit, spusťte VS Code. Vyberte **Soubor** > **Nový soubor**. 

   ![Nový soubor](./media/resource-manager-vscode-extension/new-file.png)

2. Zkopírujte a vložte do souboru následující syntaxi JSON:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "storageSKU": {
         "type": "string",
         "allowedValues": [
           "Standard_LRS",
           "Standard_ZRS",
           "Standard_GRS",
           "Standard_RAGRS",
           "Premium_LRS"
         ],
         "defaultValue": "Standard_LRS",
         "metadata": {
           "description": "The type of replication to use for the storage account."
         }
       },   
       "storageNamePrefix": {
         "type": "string",
         "maxLength": 11,
         "defaultValue": "storage",
         "metadata": {
           "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
         }
       }
     },
     "variables": {
       "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
     },
     "resources": [
       {
         "name": "[variables('storageName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "[parameters('storageSKU')]"
         },
         "kind": "Storage",
         "location": "[resourceGroup().location]",
         "tags": {},
         "properties": {
           "encryption":{
             "services":{
               "blob":{
                 "enabled":true
               }
             },
             "keySource":"Microsoft.Storage"
           }
         }
       }
     ],
     "outputs": {  }
   }
   ```

3. Uložte soubor jako **azuredeploy.json** do místní složky.

   ![Uložení šablony](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>Instalace rozšíření

1. Ve VS Code vyberte **Rozšíření**.

   ![Výběr rozšíření](./media/resource-manager-vscode-extension/select-extensions.png)

2. Vyhledejte **Nástroje Azure Resource Manageru** a vyberte **Nainstalovat**.

   ![Instalace rozšíření](./media/resource-manager-vscode-extension/install-extension.png)

3. Instalaci rozšíření dokončíte výběrem **Znovu načíst**.

## <a name="edit-the-template"></a>Úprava šablony

1. Otevřete soubor azuredeploy.json.

2. Rozšíření načte všechny dostupné [funkce šablony](resource-group-template-functions.md). Přečte také parametry a proměnné, které jste v šabloně definovali. Pokud chcete tuto funkci zobrazit, přidejte dvě hodnoty do části výstupů. V šabloně nahraďte část outputs následujícím kódem:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. Umístěte kurzor mezi uvozovky u hodnoty (value) v objektu **groupLocation**. Zadejte levou závorku (`[`). Všimněte si, že rozšíření okamžitě navrhne dostupné funkce šablony.

   ![Zobrazení dostupných funkcí](./media/resource-manager-vscode-extension/available-functions.png)

4. Začněte zadávat **resourceGroup**. Až se zobrazí funkce `resourceGroup()`, stiskněte tabulátor nebo Enter.

   ![Výběr funkce resourceGroup](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. Rozšíření vyplní syntaxi funkce. Funkce [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) nepřijímá žádné parametry. Za pravou závorkou přidejte tečku. Rozšíření nabídne vlastnosti, které jsou k dispozici pro objekt vrácený funkcí `resourceGroup()`. Vyberte `location`.

   ![Výběr vlastností](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. Za **location** přidejte pravou závorku.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. Nyní umístěte kurzor mezi uvozovky u **storageUri**. Opět zadejte levou závorku. Začněte zadávat **reference**. Až tato funkce bude vybrána, stiskněte tabulátor nebo Enter.

   ![Výběr funkce reference](./media/resource-manager-vscode-extension/add-reference.png)

8. Funkce [reference](resource-group-template-functions-resource.md#reference) jako parametr přijímá ID nebo název prostředku. Název účtu úložiště už máte v proměnné. Zadejte **var** a pak stiskněte Ctrl+mezerník. Rozšíření navrhne funkci variables.

   ![Výběr proměnné](./media/resource-manager-vscode-extension/add-variable.png)

   Stiskněte tabulátor nebo Enter.

9. Funkce [variables](resource-group-template-functions-deployment.md#variables) vyžaduje název proměnné. Mezi závorky přidejte jednoduchou uvozovku. Rozšíření nabídne názvy proměnných, které jste v šabloně definovali.

   ![Zobrazení proměnných](./media/resource-manager-vscode-extension/show-variables.png) 

10. Vyberte proměnnou **storageName**. Přidejte pravou závorku. Následující příklad ukazuje část outputs:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
       }
   }
   ```

Finální šablona vypadá takto:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
    }
  }
}
```

## <a name="deploy-template"></a>Nasazení šablony

Jste připraveni k nasazení této šablony. Pomocí PowerShellu nebo Azure CLI vytvoříte skupinu prostředků. Potom do této skupiny prostředků nasadíte účet úložiště.

* Pokud používáte PowerShell, ze složky obsahující šablonu použijte následující příkazy:

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Pokud používáte místní instalaci Azure CLI, ze složky obsahující šablonu použijte následující příkazy:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Po dokončení nasazení se vrátí výstupní hodnoty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

Pokud používáte PowerShell, použijte:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Pokud používáte Azure CLI, použijte:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Další kroky
* Další informace o struktuře šablon najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Informace o vlastnostech pro účet úložiště najdete na stránce s [referenčními informacemi k šablonám pro účty úložiště](/azure/templates/microsoft.storage/storageaccounts).
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
