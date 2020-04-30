---
title: Vytvoření izolované databáze
description: Vytvořte Azure SQL Database izolovanou databázi pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Dotaz na databázi pomocí Editoru dotazů v Azure Portal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: b06c255f94b4d539c7c85e318238b10629cbb907
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81685155"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rychlý Start: vytvoření samostatné databáze Azure SQL Database

V tomto rychlém startu pomocí Azure Portal, skriptu PowerShellu nebo skriptu Azure CLI vytvoříte Azure SQL Database jednu databázi. Pak Dotazujte databázi pomocí **Editoru dotazů** v Azure Portal. 

[Jediná databáze](sql-database-single-database.md) je nejrychlejší a nejjednodušší možnost nasazení Azure SQL Database. V rámci [serveru SQL Database](sql-database-servers.md)můžete spravovat izolovanou databázi, která se nachází uvnitř [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v zadané oblasti Azure. V tomto rychlém startu vytvoříte novou skupinu prostředků a SQL Server pro novou databázi.

Izolovanou databázi můžete vytvořit na výpočetní úrovni *zřízené* nebo *neserverového serveru* . Zřízená databáze je předem přidělena pevná množství výpočetních prostředků, včetně procesoru a paměti, a používá jeden ze dvou [nákupních modelů](sql-database-purchase-models.md). Tento rychlý Start vytvoří zřízenou databázi pomocí nákupního modelu [založeného na Vcore](sql-database-service-tiers-vcore.md) , ale můžete také zvolit model [založený na DTU](sql-database-service-tiers-DTU.md) . 

Výpočetní vrstva bez serveru je dostupná jenom v nákupním modelu založeném na vCore a má automaticky škálované výpočetní prostředky, včetně procesoru a paměti. Pokud chcete vytvořit izolovanou databázi na výpočetní úrovni bez serveru, přečtěte si téma [Vytvoření databáze bez serveru](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

## <a name="prerequisite"></a>Požadavek

- Aktivní předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Dotazování databáze

Po vytvoření databáze můžete použít vestavěný **Editor dotazů** v Azure Portal pro připojení k databázi a dotazování na data.

1. Na portálu vyhledejte a vyberte **databáze SQL**a pak ze seznamu vyberte svou databázi.
1. Na stránce **SQL Database** pro vaši databázi v nabídce vlevo vyberte **Editor dotazů (Preview)** .
1. Zadejte přihlašovací údaje správce serveru a vyberte **OK**.
   
   ![Přihlášení k editoru dotazů](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Do podokna **Editoru dotazů** zadejte následující dotaz.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Vyberte **Spustit**a potom zkontrolujte výsledky dotazu v podokně **výsledků** .

   ![Výsledky editoru dotazů](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Zavřete stránku **Editor dotazů** a po zobrazení výzvy k zahození neuložených úprav vyberte **OK** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ponechte skupinu prostředků, server a izolovanou databázi, abyste přešli na další kroky, a Naučte se, jak se připojit a dotazovat se na databázi pomocí různých metod.

Po dokončení používání těchto prostředků můžete odstranit vytvořenou skupinu prostředků, která také odstraní Server a samostatnou databázi.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Chcete-li odstranit **myResourceGroup** a všechny jeho prostředky pomocí Azure Portal:

1. Na portálu vyhledejte a vyberte **skupiny prostředků**a v seznamu vyberte **myResourceGroup** .
1. Na stránce skupina prostředků vyberte **Odstranit skupinu prostředků**.
1. V části **Zadejte název skupiny prostředků**zadejte *myResourceGroup*a pak vyberte **Odstranit**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz rozhraní příkazového řádku Azure a použijte název vaší skupiny prostředků:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující rutinu PowerShellu s použitím názvu vaší skupiny prostředků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Další kroky

[Připojení a dotazování](sql-database-connect-query.md) databáze pomocí různých nástrojů a jazyků:
> [!div class="nextstepaction"]
> [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
> 
> [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
