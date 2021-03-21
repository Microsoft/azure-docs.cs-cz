---
title: Vytvoření vyhrazeného fondu SQL (dřív SQL DW) pomocí šablony Azure Resource Manager
description: Naučte se vytvořit fond SQL Azure synapse Analytics pomocí šablony Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460733"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Rychlý Start: vytvoření vyhrazeného fondu SQL ve službě Azure synapse Analytics (dřív SQL DW) pomocí šablony ARM

Tato šablona Azure Resource Manager (šablona ARM) vytvoří vyhrazený fond SQL (dřív SQL DW) s povoleným transparentní šifrování dat. Vyhrazený fond SQL (dřív SQL DW) odkazuje na funkce podnikového datového skladu, které jsou všeobecně dostupné v Azure synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Šablona definuje jeden prostředek:

- [Microsoft. SQL/servery](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu. Tato šablona vytvoří vyhrazený fond SQL (dřív SQL DW).
   
   [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Zadejte nebo aktualizujte následující hodnoty:

   * **Předplatné**: vyberte předplatné Azure.
   * **Skupina prostředků**: vyberte **vytvořit novou** a zadejte jedinečný název pro skupinu prostředků a vyberte **OK**. Nová skupina prostředků vám usnadní vyčištění prostředků.
   * **Oblast**: Vyberte oblast.  Například **USA – střed**.
   * **SQL Server název**: přijměte výchozí název nebo zadejte název pro název SQL Server.
   * **Přihlášení správce SQL**: zadejte uživatelské jméno správce pro SQL Server.
   * **Heslo správce SQL**: zadejte heslo správce pro SQL Server.
   * **Název datového skladu**: zadejte vyhrazený název fondu SQL.
   * **Transparentní šifrování dat**: přijměte výchozí hodnotu povoleno. 
   * **Cíl na úrovni služby**: přijměte výchozí DW400c.
   * **Umístění**: přijměte výchozí umístění skupiny prostředků.
   * **Zkontrolujte a vytvořte**: vybrat.
   * **Vytvořit**: vyberte.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Můžete použít Azure Portal ke kontrole nasazených prostředků nebo k vypsání nasazených prostředků použít Azure CLI nebo skript Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

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

V tomto rychlém startu jste vytvořili vyhrazený fond SQL (dřív SQL DW) pomocí šablony ARM a ověřili nasazení. Další informace o službě Azure synapse Analytics a Azure Resource Manager najdete v následujících článcích.

- Přečtěte si [Přehled služby Azure synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Další informace o [Azure Resource Manageru](../../azure-resource-manager/management/overview.md)
- [Vytvoření a nasazení první šablony ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
