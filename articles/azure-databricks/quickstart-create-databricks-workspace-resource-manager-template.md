---
title: Rychlý Start – vytvoření pracovního prostoru Azure Databricks podle Azure Resource Manager šablony
description: V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager k vytvoření pracovního prostoru Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/27/2020
ms.openlocfilehash: 1fe0eb0a69e8522fa0eb8683e2eb706f3e60b92c
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433024"
---
# <a name="quickstart-create-an-azure-databricks-workspace-by-using-the-azure-resource-manager-template"></a>Rychlý Start: vytvoření pracovního prostoru Azure Databricks pomocí šablony Azure Resource Manager

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru Azure Databricks. Po vytvoření pracovního prostoru můžete nasazení ověřit.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Předplatné Azure – vytvořte si [ho zdarma](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-databricks-workspace/).

:::code language="json" source="~/quickstart-templates/101-databricks-workspace/azuredeploy.json" range="1-53" highlight="33-46":::

Prostředek Azure definovaný v šabloně je [Microsoft. datacihly/pracovní prostory:](/azure/templates/microsoft.databricks/workspaces): vytvořit Azure Databricks pracovní prostor.

### <a name="deploy-the-template"></a>Nasazení šablony

V této části pomocí šablony Azure Resource Manageru vytvoříte pracovní prostor Azure Databricks.

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří pracovní prostor Azure Databricks.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json)

2. Zadejte požadované hodnoty pro vytvoření pracovního prostoru Azure Databricks.

   ![Vytvoření Azure Databricksho pracovního prostoru pomocí šablony Azure Resource Manager](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Vytvoření Azure Databricksho pracovního prostoru pomocí šablony Azure Resource Manager")

   Zadejte následující hodnoty:

   |Vlastnost  |Popis  |
   |---------|---------|
   |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
   |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/management/overview.md). |
   |**Umístění**     | Vyberte **USA – východ 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
   |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
   |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Vyberte **Zkontrolovat a vytvořit** a potom **Vytvořit**.

4. Vytvoření pracovního prostoru trvá několik minut. V případě selhání nasazení pracovního prostoru je pracovní prostor stále vytvořen ve stavu selhání. Odstraňte neúspěšný pracovní prostor a vytvořte nový pracovní prostor, který vyřeší chyby nasazení. Při odstranění neúspěšného pracovního prostoru se odstraní také spravovaná skupina prostředků a všechny úspěšně nasazené prostředky.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Můžete použít Azure Portal k zkontrolování pracovního prostoru Azure Databricks nebo k vypsání prostředku pomocí následujícího skriptu Azure CLI nebo Azure PowerShell.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Databricks workspace name:" &&
read databricksWorkspaceName &&
echo "Enter the resource group where the Azure Databricks workspace exists:" &&
read resourcegroupName &&
az databricks workspace show -g $resourcegroupName -n $databricksWorkspaceName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Databricks workspace exists"
(Get-AzResource -ResourceType "Microsoft.Databricks/workspaces" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat k dalším kurzům, možná budete chtít tyto prostředky ponechat na místě. Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní Azure Databricks pracovní prostor a související spravované prostředky. Odstranění skupiny prostředků pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell:

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Databricks pracovní prostor pomocí Azure Resource Manager šablony a ověřili nasazení. V dalším článku se dozvíte, jak pomocí Azure Databricks provést operaci ETL (extrakce, transformace a načítání dat).

> [!div class="nextstepaction"]
> [Extrakce, transformace a načítání dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
