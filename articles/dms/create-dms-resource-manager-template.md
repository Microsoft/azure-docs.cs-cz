---
title: Vytvoření instance DMS (Azure Resource Manager šablona)
description: Naučte se vytvářet Database Migration Service pomocí šablony Azure Resource Manager (šablona ARM).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963211"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Rychlý Start: vytvoření instance Azure Database Migration Service pomocí šablony ARM

Tuto šablonu Azure Resource Manager (šablonu ARM) použijte k nasazení instance Azure Database Migration Service. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Šablona Azure Database Migration Service ARM vyžaduje následující: 

- Nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo [PowerShellu](/powershell/scripting/install/installing-powershell). 
- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

V šabloně jsou definovány tři prostředky Azure: 

- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): vytvoří virtuální síť. 
- [Microsoft. Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): vytvoří podsíť. 
- [Migrace a služby společnosti Microsoft. DataServices](/azure/templates/microsoft.datamigration/services): nasadí instanci Azure Database Migration Service. 

Další šablony služby Azure Database Migration Services najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří instanci Azure Database Migration Service. 

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: Vyberte existující skupinu prostředků z rozevírací nabídky nebo vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. 
    * **Oblast**: umístění, kam budou prostředky nasazeny.
    * **Název služby**: název nové migrační služby.
    * **Umístění**: umístění skupiny prostředků, ponechte výchozí hodnotu `[resourceGroup().location]` .
    * **Název virtuální** sítě: název nové virtuální sítě.
    * **Název podsítě**: název nové podsítě přidružené k virtuální síti.



3. Vyberte **Zkontrolovat a vytvořit**. Po úspěšném nasazení instance Azure Database Migration Service obdržíte oznámení. 


K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

K ověření nasazených prostředků můžete použít rozhraní příkazového řádku Azure. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků pomocí Azure CLI nebo Azure PowerShell:

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

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [ Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Další způsoby nasazení Azure Database Migration Service najdete v následujících tématech: 
- [Azure Portal](quickstart-create-data-migration-service-portal.md)

Další informace najdete v tématu [přehled Azure Database Migration Service](dms-overview.md) .