---
title: Vytvoření izolované databáze
description: Vytvořte v Azure SQL Database izolovanou databázi pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. Dotaz na databázi pomocí Editoru dotazů v Azure Portal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: 1ae3159365e63159b8ed62c09bc20441ba65d8aa
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343842"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rychlý Start: vytvoření samostatné databáze Azure SQL Database

V tomto rychlém startu pomocí Azure Portal, skriptu PowerShellu nebo skriptu Azure CLI vytvoříte v Azure SQL Database jednu databázi. Pak Dotazujte databázi pomocí **Editoru dotazů** v Azure Portal.

[Jediná databáze](single-database-overview.md) je nejrychlejší a nejjednodušší možnost Azure SQL Database. Na [serveru](logical-servers.md)můžete spravovat izolovanou databázi, která se nachází v zadané oblasti Azure v rámci [skupiny prostředků Azure](../../active-directory-b2c/overview.md) . V tomto rychlém startu vytvoříte novou skupinu prostředků a server pro novou databázi.

Izolovanou databázi můžete vytvořit na výpočetní úrovni *zřízené* nebo *neserverového serveru* . Zřízená databáze je předem přidělena pevná množství výpočetních prostředků, včetně procesoru a paměti, a používá jeden ze dvou [nákupních modelů](purchasing-models.md). Tento rychlý Start vytvoří zřízenou databázi pomocí nákupního modelu [založeného na Vcore](service-tiers-vcore.md) , ale můžete také zvolit model [založený na DTU](service-tiers-dtu.md) .

Výpočetní vrstva bez serveru je dostupná jenom v nákupním modelu založeném na vCore a má přidaný rozsah výpočetních prostředků, včetně procesoru a paměti. Pokud chcete vytvořit izolovanou databázi na výpočetní úrovni bez serveru, přečtěte si téma [Vytvoření databáze bez serveru](serverless-tier-overview.md#create-a-new-database-in-the-serverless-compute-tier).

## <a name="prerequisite"></a>Požadavek

- Aktivní předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Dotazování databáze

Po vytvoření databáze můžete použít vestavěný **Editor dotazů** v Azure Portal pro připojení k databázi a dotazování na data.

1. Na portálu vyhledejte a vyberte **databáze SQL**a pak ze seznamu vyberte svou databázi.
1. Na stránce **SQL Database** pro vaši databázi v nabídce vlevo vyberte **Editor dotazů (Preview)** .
1. Zadejte přihlašovací údaje správce serveru a vyberte **OK**.

   ![Přihlášení k editoru dotazů](./media/single-database-create-quickstart/query-editor-login.png)

1. Do podokna **Editoru dotazů** zadejte následující dotaz.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Vyberte **Spustit**a potom zkontrolujte výsledky dotazu v podokně **výsledků** .

   ![Výsledky editoru dotazů](./media/single-database-create-quickstart/query-editor-results.png)

1. Zavřete stránku **Editor dotazů** a po zobrazení výzvy k zahození neuložených úprav vyberte **OK** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ponechte skupinu prostředků, server a izolovanou databázi, abyste přešli na další kroky, a Naučte se, jak se připojit a dotazovat se na databázi pomocí různých metod.

Po dokončení používání těchto prostředků můžete odstranit vytvořenou skupinu prostředků, která také odstraní Server a samostatnou databázi.

### <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li odstranit **myResourceGroup** a všechny jeho prostředky pomocí Azure Portal:

1. Na portálu vyhledejte a vyberte **skupiny prostředků**a v seznamu vyberte **myResourceGroup** .
1. Na stránce skupina prostředků vyberte **Odstranit skupinu prostředků**.
1. V části **Zadejte název skupiny prostředků**zadejte *myResourceGroup*a pak vyberte **Odstranit**.

### <a name="the-azure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

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

[Připojení a dotazování](connect-query-content-reference-guide.md) databáze pomocí různých nástrojů a jazyků:
> [!div class="nextstepaction"]
> [Připojení a dotazování pomocí SQL Server Management Studia](connect-query-ssms.md)
>
> [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
 