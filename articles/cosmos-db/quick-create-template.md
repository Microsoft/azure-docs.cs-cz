---
title: Rychlý Start – vytvoření Azure Cosmos DB a kontejneru pomocí šablony Azure Resource Manager
description: Rychlý Start ukazující, jak se databáze Azure Cosmos a kontejneru pomocí šablony Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: f524a1e1db426b9b9dafb2fb95d77538a34b04ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605463"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Rychlý Start: vytvoření Azure Cosmos DB a kontejneru pomocí šablony Azure Resource Manager

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytvoření a dotazování databází klíčů a hodnot, databází dokumentů a grafů. Tento rychlý Start se zaměřuje na proces nasazení šablony Správce prostředků pro vytvoření databáze Azure Cosmos a kontejneru v rámci této databáze. Později můžete do tohoto kontejneru ukládat data.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Předplatné Azure nebo bezplatný zkušební účet služby Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Vytvoření účtu Azure Cosmos, databáze, kontejneru

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

V šabloně jsou definovány tři prostředky Azure:

* [Microsoft. DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): vytvořte účet Azure Cosmos.

* [Microsoft. DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): vytvoření databáze Azure Cosmos.

* [Microsoft. DocumentDB/databaseAccounts/sqlDatabases/Containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): vytvoření kontejneru Azure Cosmos.

Další příklady šablon Azure Cosmos DB najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří účet Azure Cosmos, databázi a kontejner.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

   ![Správce prostředků šablona, Integrace Azure Cosmos DB, nasazení portálu](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    Pokud není zadaný, použijte k vytvoření prostředků Azure Cosmos výchozí hodnoty.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**.
    * **Umístění:** Vyberte prosím umístění.  Například **střed USA**.
    * **Název účtu**: zadejte název účtu Azure Cosmos. Musí být globálně jedinečný.
    * **Umístění**: zadejte umístění, kde chcete vytvořit účet Azure Cosmos. Účet Azure Cosmos může být ve stejném umístění jako skupina prostředků.
    * **Primární oblast**: primární oblast repliky pro účet Azure Cosmos.
    * **Sekundární oblast**: sekundární oblast repliky pro účet Azure Cosmos.
    * **Název databáze**: název databáze Azure Cosmos.
    * **Název kontejneru**: název kontejneru Azure Cosmos.
    * **Propustnost**: propustnost kontejneru, minimální hodnota propustnosti je 400 ru/s.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.

3. Vyberte **Koupit**. Po úspěšném nasazení účtu Azure Cosmos se zobrazí oznámení:

   ![Správce prostředků šablona, integrace Cosmos DB, nasazení oznámení portálu](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal slouží k nasazení šablony. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

Můžete buď použít Azure Portal ke kontrole účtu Azure Cosmos, databáze a kontejneru nebo k vytvoření seznamu vytvořeného tajného klíče použít následující skript Azure CLI nebo Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít pokračovat v práci s dalšími kurzy a, možná budete chtít tyto prostředky ponechat na místě.
Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní účet Azure Cosmos a související prostředky. Odstranění skupiny prostředků pomocí Azure CLI nebo Azure PowerShellu:

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Azure Cosmos, databázi a kontejner pomocí šablony Azure Resource Manager a ověřili nasazení. Další informace o Azure Cosmos DB a Azure Resource Manager najdete dál v článcích níže.

- Přečtěte si [přehled Azure Cosmos DB](introduction.md)
- Další informace o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Získat další [šablony Azure Cosmos DB správce prostředků](resource-manager-samples.md)