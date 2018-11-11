---
title: Vytvoření více instancí prostředků pomocí Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru k vytvoření více instancí prostředků Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 14dd76e60f615bce4e5b5aa52e6237615071779c
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241919"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>Kurz: Vytvoření více instancí prostředků pomocí šablon Resource Manageru

Zjistěte, jak iterovat vaši šablonu Azure Resource Manageru k vytvoření více instancí prostředku Azure. V tomto kurzu upravíte šablonu tak, aby vytvořila tři instance účtu úložiště.

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Šablony Azure pro rychlý start](https://azure.microsoft.com/resources/templates/) slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Šablona obsahuje definici prostředku Microsoft.Storage/storageAccounts. Porovnejte tuto šablonu s [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="edit-the-template"></a>Úprava šablony

Stávající šablona vytvoří jeden účet úložiště. Upravíte šablonu tak, aby vytvořila tři účty úložiště.  

Z Visual Studio Code proveďte následující čtyři změny:

![Azure Resource Manager vytvoří více instancí](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Přidejte prvek `copy` k definování prostředků účtu úložiště. V elementu copy určíte počet iterací a proměnnou pro tuto smyčku. Početní hodnota musí být kladný integer a nesmí překročit 800.
2. Funkce `copyIndex()` vrátí aktuální iteraci ve smyčce. Tento index použijete jako předponu názvu. `copyIndex()` je založen na nule. Abyste odsadili hodnotu indexu, můžete hodnotu předat do funkce copyIndex(). Například *copyIndex(1)*.
3. Odstraňte prvek **proměnné**, protože se už nepoužívá.
4. Odstraňte prvek **výstupu**. Už není zapotřebí.

Hotová šablona vypadá takto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Další informace o vytvoření více instancí najdete v tématu [Nasazení více instancí prostředku nebo vlastnosti v šablonách Azure Resource Manageru](./resource-group-create-multiple.md)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) v rychlém startu pro Visual Studio Code.

Chcete-li vypsat všechny tři účty úložiště, vynechejte --název parametru:

# <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName
```

---

Porovnejte názvy účtů úložiště s názvem definice v šabloně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit více instancí účtu úložiště. Zatím jste vytvořili jeden účet úložiště nebo více instancí účtu úložiště. V dalším kurzu se zaměříte na vývoj šablony s více prostředky a více typy prostředků. Některé prostředky mají závislé prostředky.

> [!div class="nextstepaction"]
> [Vytvořit závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
