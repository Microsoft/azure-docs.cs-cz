---
title: 'Kurz: Elastické dotazy s využitím Azure SQL Data Warehouse | Dokumentace Microsoftu'
description: Tento kurz používá funkci elastický dotaz k dotazování Azure SQL Data Warehouse z Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 355ae1c27d0af8f77c2c9bda61c3581562050fc4
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307088"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Kurz: Použití elastického dotazu pro přístup k datům ve službě Azure SQL Data Warehouse z Azure SQL Database

Tento kurz používá funkci elastický dotaz k dotazování Azure SQL Data Warehouse z Azure SQL Database. 

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro kurz

Než začnete tento kurz, musíte mít splněné následující požadavky:

1. Nainstalovat SQL Server Management Studio (SSMS).
2. Vytvoření serveru Azure SQL pomocí databáze a datový sklad v rámci tohoto serveru.
3. Nastavení pravidel brány firewall pro přístup k serveru SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Nastavit připojení mezi instancemi SQL Data Warehouse a SQL Database 

1. Pomocí aplikace SSMS nebo jiného klienta dotazů otevřete nový dotaz pro databázi **hlavní** na logickém serveru.

2. Vytvořte přihlášení a uživatele, který představuje databázi SQL data warehouse připojení.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Pomocí aplikace SSMS nebo jiného klienta dotazů otevřete nový dotaz **instanci SQL data warehouse** na logickém serveru.

4. Vytvořte uživatele na instanci datového skladu pomocí přihlášení, které jste vytvořili v kroku 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Udělení oprávnění pro uživatele z kroku 4, který chcete databáze SQL se má provést. V tomto příkladu pouze se udělení oprávnění vybrat na určité schéma znázorňující, jak jsme může omezit dotazů z databáze SQL ke konkrétní doméně. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Pomocí aplikace SSMS nebo jiného klienta dotazů otevřete nový dotaz **instanci SQL database** na logickém serveru.

7. Pokud již nemáte, vytvořte hlavní klíč. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Vytvoření přihlašovacích údajů s rozsahem databáze pomocí přihlašovacích údajů, které jste vytvořili v kroku 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Vytvoření externího zdroje dat, která odkazuje na instanci datového skladu.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Nyní můžete vytvořit externí tabulky, které odkazují na tento zdroj dat externí. Dotazy, které používají tyto tabulky se posílají instanci datového skladu ke zpracování a odesílaných zpět do instance databáze.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Elastický dotaz SQL Database do služby SQL data warehouse

V dalších několika krocích vytvoříme tabulku v našich instanci datového skladu s více hodnotami. Potom jsme vám ukáže jak nastavit externí tabulky pro dotazování instanci datového skladu z instance databáze.

1. Pomocí aplikace SSMS nebo jiného klienta dotazů otevřete nový dotaz **SQL Data Warehouse** na logickém serveru.

2. Odeslat následující dotaz pro vytvoření **OrdersInformation** tabulky v instanci datového skladu.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Pomocí aplikace SSMS nebo jiného klienta dotazů otevřete nový dotaz **SQL database** na logickém serveru.

4. Odeslat následující dotaz pro vytvoření externí tabulky, která odkazuje na definice **OrdersInformation** tabulky v instanci datového skladu.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Podívejte se, že teď máte definici externí tabulky vaší **instanci SQL database**.

   ![elastický dotaz definici externí tabulky](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Odešlete následující dotaz, který se dotazuje instanci datového skladu. Měli byste obdržet pěti hodnot, které jste vložili v kroku 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Všimněte si, že bez ohledu na několik hodnot, trvá tento dotaz značnou dobu vrátit. Při používání elastického dotazu s datového skladu, jeden zvažte režijní náklady na zpracování dotazů a přesun přenosu. Vzdálené spuštění elastického dotazu využijte výpočetní výkon, ne čekací doba, po prioritu.

Blahopřejeme, jste nastavili naprosté základy elastického dotazu. 

## <a name="next-steps"></a>Další postup
Doporučení, najdete v tématu [osvědčené postupy pro používání elastického dotazu s Azure SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md).