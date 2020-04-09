---
title: Vytvoření několika instancí prostředku
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru k vytvoření více instancí prostředků Azure.
author: mumian
ms.date: 04/08/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 70c86c82cb28bf767da50cca20f7c1d052d4bf01
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982530"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Kurz: Vytvoření více instancí prostředků pomocí šablon ARM

Zjistěte, jak iterovat v šabloně Správce prostředků Azure (ARM) k vytvoření více instancí prostředku Azure. V tomto kurzu upravíte šablonu tak, aby vytvořila tři instance účtu úložiště.

![Azure Resource Manager vytvoří diagram více instancí](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Azure QuickStart Templates](https://azure.microsoft.com/resources/templates/) je úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V kódu sady Visual Studio vyberte **Soubor**>**otevřít soubor**.
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Šablona obsahuje definici prostředku Microsoft.Storage/storageAccounts. Porovnejte tuto šablonu s [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
5. Vyberte **Soubor**>**Uložit jako,** chcete-li soubor uložit jako **azuredeploy.json** do místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Stávající šablona vytvoří jeden účet úložiště. Upravíte šablonu tak, aby vytvořila tři účty úložiště.

Z Visual Studio Code proveďte následující čtyři změny:

![Azure Resource Manager vytvoří více instancí](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Přidejte prvek `copy` k definování prostředků účtu úložiště. V elementu copy určíte počet iterací a proměnnou pro tuto smyčku. Početní hodnota musí být kladný integer a nesmí překročit 800.
2. Funkce `copyIndex()` vrátí aktuální iteraci ve smyčce. Tento index použijete jako předponu názvu. `copyIndex()` je založen na nule. Abyste odsadili hodnotu indexu, můžete hodnotu předat do funkce copyIndex(). Například *copyIndex(1)*.
3. Odstraňte prvek **proměnné**, protože se už nepoužívá.
4. Odstraňte prvek **výstupu**. Už není zapotřebí.

Hotová šablona vypadá takto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Další informace o vytváření více instancí najdete [v tématu Nasazení více instancí prostředku nebo vlastnosti v šablonách ARM.](./copy-resources.md)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) v rychlém startu pro Visual Studio Code.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li vypsat všechny tři účty úložiště, vynechejte --název parametru:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Porovnejte názvy účtů úložiště s názvem definice v šabloně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit více instancí účtu úložiště.  V dalším kurzu se zaměříte na vývoj šablony s více prostředky a více typy prostředků. Některé prostředky mají závislé prostředky.

> [!div class="nextstepaction"]
> [Vytvořit závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md)
