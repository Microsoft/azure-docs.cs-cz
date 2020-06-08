---
title: Rychlý Start – vytvoření pracovního prostoru Azure Databricks pomocí prostředí PowerShell
description: V tomto rychlém startu se dozvíte, jak pomocí PowerShellu vytvořit pracovní prostor Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485728"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Rychlý Start: vytvoření pracovního prostoru Azure Databricks pomocí prostředí PowerShell

V tomto rychlém startu se dozvíte, jak pomocí PowerShellu vytvořit pracovní prostor Azure Databricks. PowerShell můžete použít k interaktivnímu vytváření a správě prostředků Azure nebo ve skriptech.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když je modul PowerShell AZ. datacihly ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.Databricks -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. datacihly všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně k dispozici v rámci Azure Cloud Shell.

Pokud Azure Databricks používáte poprvé, musíte zaregistrovat poskytovatele prostředků **Microsoft. datacihly** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) vyberte konkrétní ID předplatného.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem **myresourcegroup** v oblasti **západní USA 2** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte Azure Databricks pracovní prostor pomocí prostředí PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Zadejte následující hodnoty:

|       **Vlastnost**       |                                                                                **Popis**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Name                     | Zadejte název pracovního prostoru Databricks.                                                                                                                                   |
| ResourceGroupName        | Zadejte název existující skupiny prostředků.                                                                                                                                        |
| Umístění                 | Vyberte **USA – západ 2**. Další dostupné oblasti najdete v tématu [služby Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/regions/services/) .                                     |
| ManagedResourceGroupName | Určete, zda chcete vytvořit novou spravovanou skupinu prostředků, nebo použít existující.                                                                                        |
| Skladová jednotka (SKU)                      | Vyberte si mezi **standardem**, **Premium**nebo **zkušební verzí**. Další informace o těchto vrstvách najdete v tématu [ceny datacihlů](https://azure.microsoft.com/pricing/details/databricks/) . |

Vytvoření pracovního prostoru trvá několik minut. Po dokončení tohoto procesu se váš uživatelský účet automaticky přidá jako uživatel s právy pro správu v pracovním prostoru.

V případě selhání nasazení pracovního prostoru je pracovní prostor stále vytvořen ve stavu selhání. Odstraňte neúspěšný pracovní prostor a vytvořte nový pracovní prostor, který vyřeší chyby nasazení. Při odstranění neúspěšného pracovního prostoru se odstraní také spravovaná skupina prostředků a všechny úspěšně nasazené prostředky.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Určení stavu zřizování pracovního prostoru datacihly

Pokud chcete zjistit, jestli se pracovní prostor datacihly úspěšně zřídil, můžete použít `Get-AzDatabricksWorkspace` rutinu.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto rychlém startu nepotřebujete pro další rychlý Start nebo kurz, můžete je odstranit spuštěním následujícího příkladu.

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto rychlého startu, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Pokud chcete odstranit jenom server vytvořený v rámci tohoto rychlého startu bez odstranění skupiny prostředků, použijte `Remove-AzDatabricksWorkspace` rutinu.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření clusteru Spark ve službě Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
