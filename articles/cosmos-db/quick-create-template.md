---
title: Úvodní příručka – vytvoření databáze Azure Cosmos a kontejneru pomocí šablony Azure Resource Manager
description: Úvodní příručka o tom, jak databázi Azure Cosmos a kontejneru pomocí šablony Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: 7c02cdf772b06f7070071aa7ba35c59b019187cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78301722"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Úvodní příručka: Vytvoření databáze Azure Cosmos a kontejneru pomocí šablony Azure Resource Manager

Databáze Azure Cosmos je databázová služba Microsoftu s více modely použitelná v celosvětovém měřítku. Azure Cosmos DB můžete použít k rychlému vytváření a dotazování databází klíčů a hodnot, databází dokumentů a databází grafů. Tento rychlý start se zaměřuje na proces nasazování šablony Správce prostředků k vytvoření databáze Azure Cosmos a kontejneru v rámci této databáze. Později můžete ukládat data v tomto kontejneru.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Předplatné Azure nebo bezplatný zkušební účet Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Vytvoření účtu Azure Cosmos, databáze, kontejneru

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

V šabloně jsou definovány tři prostředky Azure:

* [Microsoft.DocumentDB/databaseAccounts:](/azure/templates/microsoft.documentdb/databaseaccounts)Vytvořte účet Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Vytvořte databázi Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Vytvoření kontejneru Azure Cosmos.

Další ukázky šablon Azure Cosmos DB najdete v [galerii šablon rychlého startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří účet Azure Cosmos, databázi a kontejner.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.

   ![Šablona Správce prostředků, integrace Azure Cosmos DB, nasazení portálu](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    Pokud není zadán, použijte výchozí hodnoty k vytvoření prostředků Azure Cosmos.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: Vyberte **Vytvořit nový**, zadejte jedinečný název skupiny prostředků a klepněte na tlačítko **OK**.
    * **Umístění:** Vyberte prosím umístění.  Například **střední USA**.
    * **Název účtu**: zadejte název účtu Azure Cosmos. Musí být celosvětově jedinečná.
    * **Umístění:** zadejte umístění, kde chcete vytvořit účet Azure Cosmos. Účet Azure Cosmos může být ve stejném umístění jako skupina prostředků.
    * **Primární oblast:** Primární oblast repliky pro účet Azure Cosmos.
    * **Sekundární oblast**: Sekundární oblast repliky pro účet Azure Cosmos.
    * **Název databáze**: Název databáze Azure Cosmos.
    * **Název kontejneru**: Název kontejneru Azure Cosmos.
    * **Propustnost**: Propustnost pro kontejner, minimální hodnota propustnost je 400 RU/s.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.

3. Vyberte **Koupit**. Po úspěšném nasazení účtu Azure Cosmos se zobrazí oznámení:

   ![Šablona Správce prostředků, integrace Cosmos DB, oznámení o nasazení portálu](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Portál Azure se používá k nasazení šablony. Kromě portálu Azure můžete taky použít Azure PowerShell, Azure CLI a REST API. Další metody nasazení najdete v [tématu Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

Můžete buď použít portál Azure ke kontrole účtu Azure Cosmos, databáze a kontejneru nebo použít následující Azure CLI nebo Azure PowerShell skript uvést tajný klíč vytvořený.

# <a name="cli"></a>[Cli](#tab/CLI)

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

Pokud máte v plánu pokračovat v práci s následnými a kurzy, můžete chtít ponechat tyto prostředky na místě.
Když už není potřeba, odstraňte skupinu prostředků, která odstraní účet Azure Cosmos a související prostředky. Odstranění skupiny prostředků pomocí Azure CLI nebo Azure Powershellu:

# <a name="cli"></a>[Cli](#tab/CLI)

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

V tomto rychlém startu jste vytvořili účet Azure Cosmos, databázi a kontejner pomocí šablony Azure Resource Manager a ověřili nasazení. Další informace o Azure Cosmos DB a Azure Resource Manager, pokračujte na články níže.

- Přečtěte si [přehled databáze Azure Cosmos DB](introduction.md)
- Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)
- Získejte další [šablony Azure Cosmos DB Resource Manager](resource-manager-samples.md)