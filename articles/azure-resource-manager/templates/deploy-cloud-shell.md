---
title: Nasazení šablon pomocí Cloud Shell
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure Cloud Shell. Prostředky jsou definované v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028603"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Nasazení šablon ARM z Azure Cloud Shell

Pomocí [Azure Cloud Shell](../../cloud-shell/overview.md) můžete nasadit šablonu Azure Resource Manager (šablona ARM). Můžete nasadit buď šablonu ARM, která je uložená vzdáleně, nebo šablonu ARM, která je uložená v místním účtu úložiště pro Cloud Shell.

Můžete nasadit do libovolného oboru. Tento článek ukazuje nasazení do skupiny prostředků.

## <a name="deploy-remote-template"></a>Nasadit vzdálenou šablonu

Pokud chcete nasadit externí šablonu, zadejte identifikátor URI šablony přesně stejně jako u jakéhokoli externího nasazení. Externí šablona může být v úložišti GitHubu nebo v externím účtu úložiště.

1. Otevřete příkazový řádek Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Otevření Cloud Shellu":::

1. K nasazení šablony použijte následující příkazy:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Nasazení místní šablony

Pokud chcete nasadit místní šablonu, musíte nejdřív odeslat šablonu do účtu úložiště, který je připojený k vaší Cloud Shell relaci.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte vaši skupinu prostředků služby Cloud Shell. Vzor názvů je `cloud-shell-storage-<region>`.

   ![Výběr skupiny prostředků](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Vyberte účet úložiště pro službu Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Výběr účtu úložiště":::

1. Vyberte **sdílení souborů**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Vybrat sdílené složky":::

1. Vyberte výchozí sdílení souborů pro Cloud Shell. Sdílená složka má formát názvu `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Výchozí sdílení souborů":::

1. Přidejte nový adresář, do kterého budou uloženy vaše šablony. Vyberte tento adresář.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Přidání adresáře":::

1. Vyberte **Nahrát**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Nahrát šablonu":::

1. Vyhledejte a nahrajte vaši šablonu.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Vybrat šablonu":::

1. Otevřete příkazový řádek Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Otevření Cloud Shellu":::

1. Přejděte do adresáře **clouddrive** . Přejděte do adresáře, který jste přidali pro uchovávání šablon.

1. K nasazení šablony použijte následující příkazy:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Další kroky

- Další informace o příkazech nasazení najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure CLI](deploy-cli.md) a [nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md).
- Chcete-li zobrazit náhled změn před nasazením šablony, viz [operace nasazení šablony ARM – citlivost](template-deploy-what-if.md).
