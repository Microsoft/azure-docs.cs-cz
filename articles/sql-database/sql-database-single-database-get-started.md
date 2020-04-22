---
title: Vytvoření jedné databáze
description: Vytvořte jednu databázi Azure SQL Database pomocí portálu Azure, PowerShellu nebo Azure CLI. Dotaz na databázi pomocí Editoru dotazů na webu Azure Portal.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685155"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Úvodní příručka: Vytvoření jediné databáze Azure SQL Database

V tomto rychlém startu použijete portál Azure, skript PowerShellu nebo skript Azure CLI k vytvoření jediné databáze Azure SQL Database. Potom dotaz databáze pomocí **editoru dotazů** na webu Azure Portal. 

[Jedna databáze](sql-database-single-database.md) je nejrychlejší a nejjednodušší možnost nasazení pro Azure SQL Database. Spravujete jednu databázi v rámci [serveru SQL Database](sql-database-servers.md), který je uvnitř [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v zadané oblasti Azure. V tomto rychlém startu vytvoříte novou skupinu prostředků a sql server pro novou databázi.

Můžete vytvořit jednu databázi v *zřízené* nebo *bezserverové* výpočetní vrstvy. Zřízená databáze je předem přiděleno pevné množství výpočetních prostředků, včetně procesoru a paměti, a používá jeden ze dvou [nákupních modelů](sql-database-purchase-models.md). Tento rychlý start vytvoří zřízenou databázi pomocí nákupního modelu [založeného na virtuálních jádrech,](sql-database-service-tiers-vcore.md) ale můžete také zvolit model [založený na DTU.](sql-database-service-tiers-DTU.md) 

Výpočetní vrstva bez serveru je k dispozici pouze v nákupním modelu založeném na virtuálních jádrech a má rozsah výpočetních prostředků s automatickým škálováním, včetně procesoru a paměti. Pokud chcete vytvořit jednu databázi v výpočetní vrstvě bez serveru, [přečtěte si](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)část Vytvoření databáze bez serveru .

## <a name="prerequisite"></a>Požadavek

- Aktivní předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Vytvoření jedné databáze

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Dotaz na databázi

Po vytvoření databáze můžete pomocí integrovaného **editoru dotazů** na webu Azure Portal připojit k databázi a dotazovat se na data.

1. Na portálu vyhledejte a vyberte **databáze SQL**a vyberte databázi ze seznamu.
1. Na stránce **Databáze SQL** pro databázi vyberte v levé nabídce **editor dotazů (preview).**
1. Zadejte přihlašovací údaje správce serveru a vyberte **OK**.
   
   ![Přihlášení do editoru dotazů](./media/sql-database-single-database-get-started/query-editor-login.png)

1. V podokně **Editor dotazů** zadejte následující dotaz.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Vyberte **Spustit**a potom zkontrolujte výsledky dotazu v podokně **Výsledky.**

   ![Výsledky editoru dotazů](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Zavřete stránku **editoru dotazů** a po zobrazení výzvy k zahození neuložených úprav vyberte **OK.**

## <a name="clean-up-resources"></a>Vyčištění prostředků

Udržujte skupinu prostředků, server a jednu databázi, abyste přešli k dalším krokům, a zjistěte, jak připojit databázi a dotazovat se ji různými metodami.

Po dokončení používání těchto prostředků můžete odstranit skupinu prostředků, kterou jste vytvořili, která také odstraní server a jednu databázi v něm.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Odstranění **myResourceGroup** a všech jejích prostředků pomocí portálu Azure:

1. Na portálu vyhledejte a vyberte **skupiny prostředků**a ze seznamu vyberte **myResourceGroup.**
1. Na stránce skupiny prostředků vyberte **Odstranit skupinu prostředků**.
1. V části **Zadejte název skupiny prostředků**zadejte *myResourceGroup*a vyberte **Odstranit**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz y Azure CLI pomocí názvu vaší skupiny prostředků:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li odstranit skupinu prostředků a všechny její prostředky, spusťte pomocí názvu skupiny prostředků následující rutinu prostředí PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Další kroky

[Připojte databázi a zařazujte se tak](sql-database-connect-query.md) pomocí různých nástrojů a jazyků:
> [!div class="nextstepaction"]
> [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
> 
> [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
