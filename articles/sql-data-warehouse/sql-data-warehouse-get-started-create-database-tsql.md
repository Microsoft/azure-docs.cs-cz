---
title: "Vytvoření SQL Data Warehouse pomocí TSQL | Dokumentace Microsoftu"
description: "Naučte se vytvářet Azure SQL Data Warehouse pomocí TSQL."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 10d8aa2b3ab8d7d8a9b91e95ffccf03faa89d237
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Vytvoření databáze SQL Data Warehouse pomocí jazyka Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

V tomto článku zjistíte, jak můžete k vytvoření SQL Data Warehouse použít T-SQL.

## <a name="prerequisites"></a>Požadavky
Na začátek budete potřebovat:

* **Účet Azure**: Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatná zkušební verze Azure][Azure Free Trial] nebo [Kredity Azure pro předplatitele MSDN][MSDN Azure Credits].
* **Azure SQL Server:** Další podrobnosti najdete v článku [Vytvoření logického serveru Azure SQL Database pomocí webu Azure Portal][Vytvoření logického serveru Azure SQL Database pomocí webu Azure Portal] nebo [Vytvoření logického serveru Azure SQL Database pomocí PowerShellu][Vytvoření logického serveru Azure SQL Database pomocí PowerShellu].
* **Skupinu prostředků**: Buď použijte stejnou skupinu prostředků jako pro Azure SQL Server, nebo zjistěte, [jak vytvořit skupinu prostředků][how to create a resource group].
* **Prostředí ke spuštění T-SQL**: Ke spuštění T-SQL můžete použít [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] nebo [SSMS][SSMS].

> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba.  Další podrobnosti o cenách najdete v tématu [SQL Data Warehouse – ceny][SQL Data Warehouse pricing].
>
>

## <a name="create-a-database-with-visual-studio"></a>Vytvoření databáze pomocí sady Visual Studio
Pokud jste ještě sadu Visual Studio nikdy nepoužívali, přečtěte si článek [Dotazování služby Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  Začněte tím, že otevřete Průzkumník objektů systému SQL Server v sadě Visual Studio a připojíte se k serveru, který bude hostitelem databáze SQL Data Warehouse.  Po připojení můžete vytvořit SQL Data Warehouse spuštěním následujícího příkazu SQL na **hlavní** databázi.  Tento příkaz vytvoří databázi MySqlDwDb s cílem služby DW400 a umožní zvětšování databáze až do maximální velikosti 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Vytvoření databáze pomocí sqlcmd
Alternativně můžete spustit stejný příkaz pomocí sqlcmd spuštěním následujícího příkazu na příkazovém řádku.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Pokud není uvedeno COLLATE, je výchozí kolace SQL_Latin1_General_CP1_CI_AS.  `MAXSIZE` může být v rozsahu od 250 GB do 240 TB.  `SERVICE_OBJECTIVE` může být v rozmezí od DW100 do DW2000 [DWU][DWU].  Seznam všech platných hodnot najdete v dokumentaci MSDN pro příkaz [CREATE DATABASE][CREATE DATABASE].  Parametr MAXSIZE i parametr SERVICE_OBJECTIVE je možné měnit pomocí příkazu [ALTER DATABASE][ALTER DATABASE] jazyka T-SQL.  Kolaci databáze nejde po vytvoření změnit.   Při změně hodnoty parametru SERVICE_OBJECTIVE je potřeba postupovat opatrně, protože změna DWU způsobí restartování služeb, což bude znamenat zrušení všech probíhajících dotazů.  Změna hodnoty parametru MAXSIZE nerestartuje služby, protože jde pouze o operaci s metadaty.

## <a name="next-steps"></a>Další kroky
Až se vám zřídí SQL Data Warehouse, můžete [načíst ukázková data][load sample data] nebo se můžete podívat, jak na [vývoj][develop], [načítání][load] nebo [migraci][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
