---
title: "Začínáme s dotazy mezidatabázové (vertikální dělení) | Microsoft Docs"
description: "pomocí dotazu elastické databáze s svisle na oddíly databáze"
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/23/2016
ms.author: sstein
ms.openlocfilehash: 0731ddfea83d287c88118cf4b48e86427d8f520f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Začínáme s dotazy mezidatabázové (vertikální dělení) (preview)
Dotaz elastické databáze (preview) pro databázi SQL Azure umožňuje spouštět dotazy T-SQL, které jsou rozmístěny v několika databází pomocí jednoho připojení bodu. Toto téma se týká [svisle na oddíly databáze](sql-database-elastic-query-vertical-partitioning.md).  

Po dokončení bude: Zjistěte, jak konfigurovat a používat Azure SQL Database provádět dotazy, které jsou rozmístěny v několika související databází. 

Další informace o funkci dotazu elastické databáze, najdete v tématu [přehled dotazu elastické databáze Azure SQL Database](sql-database-elastic-query-overview.md). 

## <a name="prerequisites"></a>Požadavky

Musíte mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE. K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

## <a name="create-the-sample-databases"></a>Vytvoření ukázkové databáze
Abyste mohli začít, potřebujeme vytvořit dvě databáze, **zákazníci** a **objednávky**, buď na stejný nebo jiný logického serveru.   

Spusťte tyto dotazy na **objednávky** databáze slouží k vytvoření **OrderInformation** tabulky a vstup ukázková data. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Nyní, spustit následující dotaz na **zákazníci** databáze slouží k vytvoření **CustomerInformation** tabulky a vstup ukázková data. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Vytvoření databázových objektů
### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč a přihlašovací údaje na obor definovaný databází
1. Spusťte aplikaci SQL Server Management Studio nebo SQL Server Data Tools v sadě Visual Studio.
2. Připojení k databázi objednávek a spuštěním následujících příkazů T-SQL:
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    "Username" a "password" by měl být uživatelské jméno a heslo použité k přihlášení do databáze zákazníků.
    Ověřování pomocí služby Azure Active Directory s elastické dotazy není aktuálně podporován.

### <a name="external-data-sources"></a>Externí zdroje dat
Pokud chcete vytvořit externího zdroje dat, spusťte následující příkaz v databázi objednávky: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Externí tabulky
Vytvoření externí tabulky v databázi objednávky, který odpovídá definici tabulky CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového dotazu T-SQL elastické databáze
Jakmile definujete zdroj externích dat a externí tabulky teď můžete T-SQL pro dotaz na externí tabulky. Spusťte tento dotaz na databázi objednávky: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Náklady
V současné době funkce dotazu elastické databáze je zahrnut do náklady na vaší databázi SQL Azure.  

Informace o cenách najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database). 

## <a name="next-steps"></a>Další postup

* Přehled elastické dotazů najdete v tématu [elastické dotazu přehled](sql-database-elastic-query-overview.md).
* Syntaxe a ukázkové dotazy pro svisle oddílů data, najdete v části [dotazování svisle na oddíly dat)](sql-database-elastic-query-vertical-partitioning.md)
* Podívejte se kurz vodorovné rozdělení do oddílů (horizontálního dělení) [Začínáme s elastické dotazu pro vodorovné rozdělení do oddílů (horizontálního dělení)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělenou data, najdete v části [dotazování vodorovně rozdělena na oddíly dat)](sql-database-elastic-query-horizontal-partitioning.md)
* V tématu [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, který spouští příkaz jazyka Transact-SQL na jedné vzdálené databáze SQL Azure nebo sadu databází, které slouží jako horizontálních oddílů v vodorovné schéma rozdělení oddílů.
