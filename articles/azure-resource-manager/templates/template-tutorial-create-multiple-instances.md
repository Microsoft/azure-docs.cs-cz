---
title: Vytvoření několika instancí prostředku
description: Naučte se vytvořit šablonu Azure Resource Manager (šablonu ARM) pro vytvoření více instancí prostředků Azure.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: e669e27547633639a88674ffee499fb1d84facdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673949"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Kurz: Vytváření více instancí prostředků pomocí šablon ARM

Přečtěte si, jak iterovat šablonu Azure Resource Manager (šablonu ARM) a vytvořit víc instancí prostředku Azure. V tomto kurzu upravíte šablonu tak, aby vytvořila tři instance účtu úložiště.

![Azure Resource Manager vytvoří diagram více instancí.](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

Microsoft Learn modul, který pokrývá kopírování prostředků, najdete v tématu [Správa složitých nasazení cloudu pomocí pokročilých funkcí šablon ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/) jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. Z Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.
1. `Microsoft.Storage/storageAccounts`V šabloně je definován prostředek. Porovnejte tuto šablonu s [referenčními informacemi k šablonám](/azure/templates/Microsoft.Storage/storageAccounts). Před přizpůsobením šablony je užitečné získat základní informace o této šabloně.
1. Vyberte **soubor**  >  **Uložit jako** a uložte soubor jako _azuredeploy.js_ do svého místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Stávající šablona vytvoří jeden účet úložiště. Upravíte šablonu tak, aby vytvořila tři účty úložiště.

Z Visual Studio Code proveďte následující čtyři změny:

![Azure Resource Manager vytvoří více instancí](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Přidejte prvek `copy` k definování prostředků účtu úložiště. V `copy` elementu zadejte počet iterací a proměnnou pro tuto smyčku. Početní hodnota musí být kladný integer a nesmí překročit 800.
2. Funkce `copyIndex()` vrátí aktuální iteraci ve smyčce. Tento index použijete jako předponu názvu. `copyIndex()` je založen na nule. K posunutí hodnoty indexu můžete předat hodnotu ve `copyIndex()` funkci. Například, `copyIndex(1)`.
3. Odstraňte `variables` element, protože už se nepoužívá.
4. Odstraňte `outputs` element. Už to není potřeba.

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

Další informace o vytváření více instancí najdete v tématu [iterace prostředků v šablonách ARM](./copy-resources.md) .

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte své preferované prostředí. Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Viz předchozí snímek obrazovky. Vyberte soubor, který jste uložili v předchozí části. Po nahrání souboru můžete pomocí `ls` příkazu a `cat` příkazu ověřit, jestli se soubor úspěšně nahrál.

1. Z Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Po úspěšném nasazení šablony můžete zobrazit tři účty úložiště vytvořené v zadané skupině prostředků. Porovnejte názvy účtů úložiště s názvem definice v šabloně.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  V rámci skupiny prostředků se zobrazí celkem tři prostředky.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit více instancí účtu úložiště. V dalším kurzu se zaměříte na vývoj šablony s více prostředky a více typy prostředků. Některé prostředky mají závislé prostředky.

> [!div class="nextstepaction"]
> [Vytvořit závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md)
