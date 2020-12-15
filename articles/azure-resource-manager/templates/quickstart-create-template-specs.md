---
title: Vytvoření a nasazení specifikace šablony
description: Naučte se vytvořit specifikaci šablony ze šablony ARM. Pak nasaďte specifikaci šablony do skupiny prostředků v rámci vašeho předplatného.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 1d3e7c6ee6d19d4d2cd3828d5abf95ccb5457e76
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511344"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Rychlý Start: vytvoření a nasazení specifikace šablony (Preview)

V tomto rychlém startu se dozvíte, jak zabalit šablonu Azure Resource Manager (šablonu ARM) do [specifikace šablony](template-specs.md). Pak nasadíte tuto specifikaci šablony. Vaše specifikace šablony obsahuje šablonu ARM, která nasazuje účet úložiště.

## <a name="prerequisites"></a>Požadavky

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specifikace šablony jsou momentálně ve verzi Preview. Pokud ho chcete použít s Azure PowerShell, musíte nainstalovat [verzi 5.0.0 nebo novější](/powershell/azure/install-az-ps). Pokud ho chcete použít v Azure CLI, použijte [verzi 2.14.2 nebo novější](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Vytvoření šablony

Vytvoříte specifikaci šablony z místní šablony. Zkopírujte následující šablonu a uložte ji místně do souboru s názvem **azuredeploy.jsv**. Tento rychlý Start předpokládá, že jste uložili do cesty **c:\Templates\azuredeploy.js** , ale můžete použít libovolnou cestu.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Vytvořit specifikaci šablony

Specifikace šablony je typ prostředku s názvem `Microsoft.Resources/templateSpecs` . K vytvoření specifikace šablony použijte PowerShell, rozhraní příkazového řádku Azure, portál nebo šablonu ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte novou skupinu prostředků, která bude obsahovat specifikaci šablony.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Vytvořte v této skupině prostředků specifikaci šablony. Dejte nové specifikaci šablony název **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

1. Vytvořte novou skupinu prostředků, která bude obsahovat specifikaci šablony.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Vytvořte v této skupině prostředků specifikaci šablony. Dejte nové specifikaci šablony název **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte **specifikace šablony**. Z dostupných možností vyberte možnost **šablony** .

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="Hledat specifikace šablony":::

1. Vyberte **Importovat šablonu**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="Importovat šablonu":::

1. Vyberte ikonu složky.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="otevřít složku":::

1. Přejděte na místní šablonu, kterou jste uložili, a vyberte ji. Vyberte **Otevřít**.
1. Vyberte **Importovat**.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="vybrat možnost importu":::

1. Zadejte následující hodnoty:

    - **Název**: zadejte název specifikace šablony.  Například **storageSpec**
    - **Předplatné**: vyberte předplatné Azure, které se používá k vytvoření specifikace šablony.
    - **Skupina prostředků**: vyberte **vytvořit novou** a potom zadejte nový název skupiny prostředků.  Například **templateSpecRG**.
    - **Umístění:**: Vyberte umístění pro skupinu prostředků. Například  **západní USA 2**.
    - **Verze**: Zadejte verzi specifikace šablony. použijte **1,0**.

1. Vyberte **zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit**.

# <a name="arm-template"></a>[Šablona ARM](#tab/azure-resource-manager)

> [!NOTE]
> Místo použití šablony ARM doporučujeme, abyste k vytvoření specifikace šablony použili PowerShell nebo rozhraní příkazového řádku. Tyto nástroje automaticky převádějí propojené šablony na artefakty připojené k vaší hlavní šabloně. Když použijete šablonu ARM k vytvoření specifikace šablony, musíte tyto propojené šablony ručně přidat jako artefakty, které můžou být komplikované.

1. Když použijete šablonu ARM k vytvoření specifikace šablony, šablona je vložena do definice prostředků. Existují některé změny, které je třeba provést v místní šabloně. Zkopírujte následující šablonu a uložte ji místně jako **azuredeploy.jsna**.

    > [!NOTE]
    > V vložené šabloně musí být všechny [výrazy šablony](template-expressions.md) uvozeny druhou levou hranatou závorkou. `"[[`Místo použijte `"[` . Pole JSON stále používají jedinou levou závorku.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Pomocí Azure CLI nebo PowerShellu vytvořte novou skupinu prostředků.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Nasaďte šablonu pomocí Azure CLI nebo PowerShellu.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

K nasazení specifikace šablony použijte stejné příkazy pro nasazení, jako byste použili k nasazení šablony. Před nasazením předejte ID prostředku specifikace šablony.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte skupinu prostředků, která bude obsahovat nový účet úložiště.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Získejte ID prostředku specifikace šablony.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Nasaďte specifikaci šablony.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Zadejte parametry přesně stejně jako u šablony ARM. Znovu nasaďte specifikaci šablony s parametrem pro typ účtu úložiště.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

1. Vytvořte skupinu prostředků, která bude obsahovat nový účet úložiště.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Získejte ID prostředku specifikace šablony.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Došlo k známému problému s získáním ID specifikace šablony a jeho přiřazením k proměnné ve Windows PowerShellu.

1. Nasaďte specifikaci šablony.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Zadejte parametry přesně stejně jako u šablony ARM. Znovu nasaďte specifikaci šablony s parametrem pro typ účtu úložiště.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vyberte specifikaci šablony, kterou jste vytvořili.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="vybrat specifikace šablony":::

1. Vyberte **Nasadit**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="nasadit specifikace šablony":::

1. Zadejte následující hodnoty:

    - **Předplatné**: vyberte předplatné Azure pro vytvoření prostředku.
    - **Skupina prostředků**: vyberte **vytvořit novou** a potom zadejte **storageRG**.
    - **Typ účtu úložiště**: vyberte **Standard_GRS**.

1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

# <a name="arm-template"></a>[Šablona ARM](#tab/azure-resource-manager)

1. Zkopírujte následující šablonu a uložte ji místně do souboru s názvem **storage.jsv**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Pomocí Azure CLI nebo PowerShellu vytvořte novou skupinu prostředků pro účet úložiště.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Nasaďte šablonu pomocí Azure CLI nebo PowerShellu.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Udělení přístupu

Pokud chcete umožnit dalším uživatelům ve vaší organizaci nasazovat specifikace šablony, musíte jim udělit oprávnění ke čtení. Roli Čtenář můžete přiřadit skupině Azure AD pro skupinu prostředků, která obsahuje specifikace šablony, které chcete sdílet. Další informace najdete v tématu [kurz: udělení přístupu skupině k prostředkům Azure pomocí Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="update-template"></a>Aktualizovat šablonu

Řekněme, že jste identifikovali změnu, kterou chcete provést v šabloně ve specifikaci šablony. Následující šablona je podobná vaší předchozí šabloně s tím rozdílem, že přidá předponu názvu účtu úložiště. Zkopírujte následující šablonu a aktualizujte azuredeploy.jsv souboru.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Verze specifikace šablony aktualizace

Místo vytvoření nové specifikace šablony pro revidovanou šablonu přidejte novou verzi s názvem `2.0` do existující specifikace šablony. uživatelé si můžou zvolit, jak má nasazovat verze.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte novou verzi pro specifikaci šablony.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Chcete-li nasadit novou verzi, Získejte ID prostředku pro danou `2.0` verzi.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Nasaďte tuto verzi. Zadejte předponu názvu účtu úložiště.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

1. Vytvořte novou verzi pro specifikaci šablony.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Chcete-li nasadit novou verzi, Získejte ID prostředku pro danou `2.0` verzi.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Nasaďte tuto verzi. Zadejte předponu názvu účtu úložiště.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Ve specifikaci šablony vyberte **vytvořit novou verzi**.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="vytvořit novou verzi":::

1. Pojmenujte novou verzi `2.0` a volitelně přidejte poznámky. Vyberte **Upravit šablonu**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="název nové verze":::

1. Nahraďte obsah šablony aktualizovanou šablonou. Vyberte **zkontrolovat a uložit**.
1. Vyberte **Uložit změny**.

1. Pokud chcete nasadit novou verzi, vyberte **verze** .

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="verze seznamu":::

1. Pro verzi, kterou chcete nasadit, vyberte tři tečky a **Nasaďte** je.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="Vyberte verzi, kterou chcete nasadit.":::

1. Vyplňte pole jako při nasazování starší verze.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

# <a name="arm-template"></a>[Šablona ARM](#tab/azure-resource-manager)

1. Znovu je nutné provést některé změny místní šablony, aby fungovaly se specifikacemi šablon. Zkopírujte následující šablonu a uložte ji místně jako azuredeploy.jsna.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Pokud chcete přidat novou verzi do specifikace šablony, nasaďte šablonu pomocí Azure CLI nebo PowerShellu.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. Zkopírujte následující šablonu a uložte ji místně do souboru s názvem **storage.jsv**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Nasaďte šablonu pomocí Azure CLI nebo PowerShellu.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředek, který jste nasadili v rámci tohoto rychlého startu, odstraňte obě skupiny prostředků, které jste vytvořili.

1. Na webu Azure Portal vyberte v nabídce nalevo Skupina prostředků.

1. Do pole filtrovat podle názvu zadejte název skupiny prostředků (templateSpecRG a storageRG).

1. Vyberte název skupiny prostředků.

1. V horní nabídce vyberte Odstranit skupinu prostředků.

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření specifikace šablony, která obsahuje propojené šablony, najdete v tématu [Vytvoření šablony specifikace propojené šablony](template-specs-create-linked.md).
