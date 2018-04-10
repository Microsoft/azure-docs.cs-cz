---
title: Vytvoření první šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Podrobný průvodce vytvořením první šablony Azure Resource Manageru. Ukáže vám, jak vytvořit šablonu pomocí referenčních informací k šablonám pro účet úložiště.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/30/2018
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: adf7d6ad04b9c341eac2172e09da3cb1f044aa62
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Vytvoření a nasazení první šablony Azure Resource Manageru
Toto téma vás provede jednotlivými kroky k vytvoření první šablony Azure Resource Manageru. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md). Pokud máte existující prostředky a chcete pro ně získat šablonu, podívejte se na téma [Export šablony Azure Resource Manageru z existujících prostředků](resource-manager-export-template.md).

K vytváření a revidování šablon potřebujete editor JSON. [Visual Studio Code](https://code.visualstudio.com/) je jednoduchý, opensourcový editor kódu pro různé platformy. Důrazně doporučujeme k vytváření šablon Resource Manageru používat Visual Studio Code. V tomto článku se předpokládá, že používáte VS Code. Pokud máte jiný editor JSON (například sadu Visual Studio), můžete použít ten.

## <a name="prerequisites"></a>Požadavky

* Visual Studio Code. V případě potřeby ho nainstalujte z webu [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Místní nainstalovaný [Azure PowerShell](/powershell/azure/install-azurerm-ps) nebo [Azure CLI](/cli/azure/install-azure-cli). Pro účely tohoto kurzu potřebujete místní instalaci, protože vaše šablona je uložená jako místní soubor. Pokud chcete použít Cloud Shell, musíte [svou šablonu nahrát do účtu úložiště](resource-group-template-deploy-cli.md#deploy-template-from-cloud-shell).

## <a name="create-template"></a>Vytvoření šablony

Začněme jednoduchou šablonou, která do vašeho předplatného nasadí účet úložiště.

1. Vyberte **Soubor** > **Nový soubor**. 

   ![Nový soubor](./media/resource-manager-create-first-template/new-file.png)

2. Zkopírujte a vložte do souboru následující syntaxi JSON:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Pro názvy účtů úložiště platí několik omezení, proto je jejich nastavení obtížné. Název musí být dlouhý 3 až 24 znaků, obsahovat pouze číslice a malá písmena a být jedinečný. Předchozí šablona pomocí funkce [uniqueString](resource-group-template-functions-string.md#uniquestring) generuje hodnotu hash. Aby byla tato hodnota hash srozumitelnější, přidá se k ní předpona *storage*. 

3. Uložte soubor jako **azuredeploy.json** do místní složky.

   ![Uložení šablony](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Nasazení šablony

Jste připraveni k nasazení této šablony. Pomocí PowerShellu nebo Azure CLI vytvoříte skupinu prostředků. Potom do této skupiny prostředků nasadíte účet úložiště.

* Pokud používáte PowerShell, ze složky obsahující šablonu použijte následující příkazy:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Pokud používáte místní instalaci Azure CLI, ze složky obsahující šablonu použijte následující příkazy:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Jakmile se nasazení dokončí, váš účet úložiště bude existovat ve skupině prostředků.

## <a name="customize-the-template"></a>Přizpůsobení šablony

Šablona funguje bez problémů, ale není flexibilní. Pokaždé nasadí místně redundantní úložiště do oblasti Střed USA – jih. Název je vždy *storage* a hodnota hash. Pokud chcete umožnit používání šablony pro různé scénáře, přidejte do ní parametry.

Následující příklad ukazuje sekci parametrů se dvěma parametry. První parametr `storageSKU` umožňuje zadat typ redundance. Hodnoty, které můžete předat, omezuje jenom na hodnoty, které jsou platné pro účet úložiště. Také určuje výchozí hodnotu. Druhý parametr `storageNamePrefix` je nastavený tak, aby povoloval maximálně 11 znaků. Určuje výchozí hodnotu.

```json
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
```

V sekci proměnných přidejte proměnnou `storageName`. Kombinuje v sobě hodnotu předpony z parametrů a hodnotu hash z funkce [uniqueString](resource-group-template-functions-string.md#uniquestring). Pomocí funkce [toLower](resource-group-template-functions-string.md#tolower) převádí všechny znaky na malá písmena.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Pokud chcete použít tyto nové hodnoty pro účet úložiště, změňte definici prostředků:

```json
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
    "properties": {}
  }
],
```

Všimněte si, že název účtu úložiště je teď nastavený na proměnnou, kterou jste přidali. Název skladové položky je nastavený na hodnou parametru. Umístění je nastavené na stejné umístění jako skupina prostředků.

Uložte soubor. 

Vaše šablona teď vypadá nějak takto:

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
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Opětovné nasazení šablony

Znovu nasaďte šablonu s jinými hodnotami.

Pokud používáte PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Pokud používáte Azure CLI, použijte:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>Použití automatického dokončování

Dosud se vaše práce na šabloně skládala jenom z kopírování a vkládání kódu JSON z tohoto článku. Při vývoji vlastních šablon však budete chtít vyhledávat a zadávat vlastnosti a hodnoty, které jsou dostupné pro příslušný typ prostředku. VS Code přečte schéma pro typ prostředku a navrhne vlastnosti a hodnoty. Pokud chcete zobrazit funkci automatického dokončování, přejděte k elementu properties vaší šablony a přidejte nový řádek. Zadejte uvozovku a všimněte si, že VS Code okamžitě navrhne dostupné názvy v rámci elementu properties.

![Zobrazení dostupných vlastností](./media/resource-manager-create-first-template/show-properties.png)

Vyberte **encryption**. Zadejte dvojtečku (:) a VS Code navrhne přidání nového objektu.

![Přidání objektu](./media/resource-manager-create-first-template/add-object.png)

Stiskněte tabulátor nebo Enter a přidejte objekt.

Opět zadejte uvozovku a všimněte si, že VS Code teď navrhne dostupné vlastnosti pro šifrování.

![Zobrazení vlastností šifrování](./media/resource-manager-create-first-template/show-encryption-properties.png)

Vyberte **services** a pokračujte v přidávání hodnot na základě rozšíření VS Code, dokud nebudete mít:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

Povolili jste šifrování objektů blob pro účet úložiště. Editor VS Code ale zjistil problém. Všimněte si upozornění u elementu encryption.

![Upozornění u elementu encryption](./media/resource-manager-create-first-template/encryption-warning.png)

Pokud chcete zobrazit upozornění, najeďte myší na zelenou čáru.

![Chybějící vlastnost](./media/resource-manager-create-first-template/missing-property.png)

Uvidíte, že element encryption vyžaduje vlastnost keySource. Za objektem služby přidejte čárku a vlastnost keySource. VS Code navrhne jako platnou hodnotu **Microsoft.Storage**. Element properties po dokončení vypadá takto:

```json
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
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>Nasazení šifrovaného úložiště

Znovu nasaďte šablonu a zadejte nový název účtu úložiště.

Pokud používáte PowerShell, použijte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Pokud používáte Azure CLI, použijte:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

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
* Pokud chcete získat větší pomoc s vývojem šablon, můžete si nainstalovat rozšíření VS Code. Další informace najdete v tématu [Použití rozšíření Visual Studio Code k vytvoření šablony Azure Resource Manageru](resource-manager-vscode-extension.md).
* Další informace o struktuře šablon najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Informace o vlastnostech pro účet úložiště najdete na stránce s [referenčními informacemi k šablonám pro účty úložiště](/azure/templates/microsoft.storage/storageaccounts).
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
