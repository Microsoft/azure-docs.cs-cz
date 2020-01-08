---
title: Vytvoření izolované databáze
description: Vytvořte a Dotazujte izolovanou databázi v Azure SQL Database pomocí Azure Portal, PowerShellu a rozhraní příkazového řádku Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 5cfccd6e79b7289b6fd59abcabba05abab6ab09b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443842"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Rychlý Start: vytvoření izolované databáze v Azure SQL Database pomocí Azure Portal, PowerShellu a rozhraní příkazového řádku Azure

Vytvoření [jedné databáze](sql-database-single-database.md) je nejrychlejší a nejjednodušší možnost nasazení pro vytvoření databáze v Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit a pak zadat dotaz na izolovanou databázi pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/). 

Pro všechny kroky v tomto rychlém startu se přihlaste k [Azure Portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

Jedna databáze se dá vytvořit buď v zřízené, nebo na výpočetní úrovni bez serveru.

- Jedna databáze v zřízené výpočetní úrovni je předem přidělena pevná množství výpočetních prostředků, včetně procesoru a paměti, pomocí jednoho ze dvou [nákupních modelů](sql-database-purchase-models.md).
- Jedna databáze na výpočetní úrovni bez serveru obsahuje řadu výpočetních prostředků, včetně procesoru a paměti, které se automaticky škálují a jsou dostupné jenom v [nákupních modelech založených na Vcore](sql-database-service-tiers-vcore.md).

Když vytvoříte izolovanou databázi, nadefinujete taky [SQL Database Server](sql-database-servers.md) , který budete spravovat a umístit do [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v zadané oblasti.

> [!NOTE]
> V tomto rychlém startu se používá [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md), ale [nákupní model založený na DTU](sql-database-service-tiers-DTU.md) je také k dispozici.

Vytvoření samostatné databáze obsahující ukázková data AdventureWorksLT:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Dotazování databáze

Teď, když jste vytvořili databázi, použijte integrovaný nástroj pro dotazování v Azure Portal pro připojení k databázi a dotazování na data.

1. Na stránce **SQL Database** pro vaši databázi v nabídce vlevo vyberte **Editor dotazů (Preview)** .

   ![Přihlášení k editoru dotazů](./media/sql-database-get-started-portal/query-editor-login.png)

2. Zadejte přihlašovací údaje a vyberte **OK**.
3. Do podokna **Editoru dotazů** zadejte následující dotaz.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Vyberte **Spustit**a potom zkontrolujte výsledky dotazu v podokně **výsledků** .

   ![Výsledky editoru dotazů](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zavřete stránku **Editor dotazů** a po zobrazení výzvy k zahození neuložených úprav vyberte **OK** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít k [dalším krokům](#next-steps), zachovejte tuto skupinu prostředků, databázový server a samostatnou databázi. Následující kroky ukazují, jak se připojit k databázi a dotazovat se na ni pomocí různých metod.

Po dokončení používání těchto prostředků je můžete odstranit následujícím způsobem:

1. V nabídce vlevo v Azure Portal vyberte **skupiny prostředků**a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit skupinu prostředků**.
3. Do pole zadejte *myResourceGroup* a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databázi z místních nebo vzdálených nástrojů. Další informace najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).
- Po vytvoření pravidla brány firewall na úrovni serveru se [připojte a Dotazujte](sql-database-connect-query.md) databázi pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Informace o vytvoření izolované databáze v zřízené výpočetní úrovni pomocí Azure CLI najdete v [ukázkách Azure CLI](sql-database-cli-samples.md).
- Pokud chcete vytvořit izolovanou databázi v zřízené výpočetní úrovni pomocí Azure PowerShell, přečtěte si téma [Azure PowerShell Samples](sql-database-powershell-samples.md).
- Pokud chcete vytvořit izolovanou databázi na výpočetní úrovni bez serveru pomocí Azure PowerShellu, přečtěte si téma [Vytvoření databáze bez serveru](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
