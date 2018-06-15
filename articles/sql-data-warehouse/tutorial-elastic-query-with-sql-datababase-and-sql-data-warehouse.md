---
title: 'Kurz: Elastické dotaz s Azure SQL Data Warehouse | Microsoft Docs'
description: Tento kurz používá funkci elastické dotazu pro dotaz Azure SQL Data Warehouse z databáze SQL Azure.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: a31f035b5ec086a046028956c4a9c0de0d6a313d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526188"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Kurz: Použití elastické dotazu pro přístup k datům v Azure SQL Data Warehouse z databáze SQL Azure

Tento kurz používá funkci elastické dotazu pro dotaz Azure SQL Data Warehouse z databáze SQL Azure. 

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro kurz

Než začnete tento kurz, musíte mít následující požadavky:

1. Nainstalovat SQL Server Management Studio (SSMS).
2. Vytvoření serveru Azure SQL s databáze a datový sklad v rámci tohoto serveru.
3. Nastavte pravidla brány firewall pro přístup k serveru SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Nastavit připojení mezi instancemi SQL datového skladu a databáze SQL 

1. Pomocí aplikace SSMS nebo jinému dotaz klientovi, otevřete nový dotaz pro databázi **hlavní** na logickém serveru.

2. Vytvořte přihlášení a uživatele, který představuje databázi SQL data warehouse připojení.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Pomocí aplikace SSMS nebo jinému dotaz klientovi, otevřete nový dotaz **instance serveru SQL datového skladu** na logickém serveru.

4. Vytvořte uživatele na instance datového skladu s přihlášením, kterou jste vytvořili v kroku 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Udělení oprávnění pro uživatele z kroku 4, který chcete databáze SQL se má spustit. V tomto příkladu pouze se udělení oprávnění vyberte na určité schéma, ilustrující, jak jsme může omezit dotazů z databáze SQL ke konkrétní doméně. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Pomocí aplikace SSMS nebo jinému dotaz klientovi, otevřete nový dotaz **instance databáze serveru SQL** na logickém serveru.

7. Pokud není již nemáte, vytvořte hlavní klíč. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Vytvoření oboru databáze přihlašovacích údajů pomocí přihlašovacích údajů, které jste vytvořili v kroku 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Vytvořte zdroj externích dat, která odkazuje na instance datového skladu.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Nyní můžete vytvořit externí tabulky, které odkazují na tento zdroj dat externí. Dotazy pomocí těchto tabulkách jsou odesílány instance datového skladu ke zpracování a odeslána zpět do instance databáze.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Elastické dotaz z databáze SQL pro datový sklad SQL

V několika dalších krocích vytvoříme tabulku v našem instance datového skladu s několika hodnotami. Pak popisuje, jak nastavit externí tabulku k dotazování instance datového skladu z instance databáze.

1. Pomocí aplikace SSMS nebo jinému dotaz klientovi, otevřete nový dotaz **SQL Data Warehouse** na logickém serveru.

2. Odeslat následující dotaz pro vytvoření **OrdersInformation** tabulky v vaší instance datového skladu.

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

3. Pomocí aplikace SSMS nebo jinému dotaz klientovi, otevřete nový dotaz **databáze SQL** na logickém serveru.

4. Následující dotaz, který vytvořit definici externí tabulky, který odkazuje na odeslání **OrdersInformation** tabulky v instance datového skladu.

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

5. Pozorovat, že máte nyní definici externí tabulky ve vaší **instance databáze serveru SQL**.

   ![definici externí tabulky elastické dotazu](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Odešlete tento dotaz, který se dotazuje instance datového skladu. Měli byste obdržet pěti hodnot, které jste vložili v kroku 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Všimněte si, že i přes několik hodnot, trvá tento dotaz značnou dobu vrátit. Při použití elastické dotazu datového skladu, jeden měli zvážit režijní náklady na zpracování dotazů a přesun prostřednictvím sítě. Vzdálené spuštění elastické dotazu využívat výpočetní výkon, není čekací doba, po prioritu.

Blahopřejeme, jste nastavili velmi základy elastické dotazu. 

## <a name="next-steps"></a>Další postup
Doporučení, najdete v části [osvědčené postupy pro použití elastické dotazu s Azure SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md).