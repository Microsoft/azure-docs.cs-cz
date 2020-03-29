---
title: Začínáme s dotazy napříč databázemi
description: použití elastického databázového dotazu se svisle rozdělenými databázemi
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823798"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Začínáme s dotazy mezi databázemi (vertikální dělení) (náhled)

Elastický databázový dotaz (preview) pro Azure SQL Database umožňuje spouštět dotazy T-SQL, které pokrývají více databází pomocí jednoho spojovacího bodu. Tento článek se vztahuje na [svisle rozdělené databáze](sql-database-elastic-query-vertical-partitioning.md).  

Po dokončení se dozvíte, jak nakonfigurovat a používat Azure SQL Database k provádění dotazů, které pokrývají více souvisejících databází.

Další informace o funkci elastického databázového dotazu naleznete v tématu [Přehled elastického databázového dotazu Azure SQL Database](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Požadavky

Je vyžadováno změnit oprávnění externího zdroje dat. Toto oprávnění je součástí oprávnění ALTER DATABASE. ZMĚNIT všechna oprávnění externího zdroje dat jsou potřebné k odkazování na základní zdroj dat.

## <a name="create-the-sample-databases"></a>Vytvoření ukázkových databází

Chcete-li začít s, vytvořte dvě databáze, **Zákazníci** a **Objednávky**, buď ve stejné nebo různé servery DATABÁZE SQL.

Proveďte následující dotazy v databázi **Orders** k vytvoření tabulky **OrderInformation** a zadání ukázkových dat.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Nyní spusťte následující dotaz v **databázi Zákazníci** k vytvoření **customerinformation** tabulka a zadejte ukázková data.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Vytváření databázových objektů

### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč a pověření s rozsahem databáze

1. Otevřete sql server management studio nebo SQL Server datové nástroje v sadě Visual Studio.
2. Připojte se k databázi Orders a proveďte následující příkazy T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "Uživatelské jméno" a "heslo" by mělo být uživatelské jméno a heslo používané pro přihlášení do databáze Zákazníci.
    Ověřování pomocí služby Azure Active Directory s elastické dotazy není aktuálně podporováno.

### <a name="external-data-sources"></a>Externí zdroje dat

Chcete-li vytvořit externí zdroj dat, proveďte v databázi Objednávky následující příkaz:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Externí tabulky

Vytvořte externí tabulku v databázi Objednávky, která odpovídá definici tabulky CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového elastického dotazu T-SQL

Jakmile definujete externí zdroj dat a externí tabulky, můžete nyní použít T-SQL k dotazování externích tabulek. Spusťte tento dotaz v databázi Objednávky:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Náklady

V současné době je funkce elastického databázového dotazu zahrnuta do nákladů na databázi Azure SQL Database.  

Informace o cenách naleznete v [tématu SQL Database Pricing](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v [tématu Přehled elastického dotazu](sql-database-elastic-query-overview.md).
* Syntaxe a ukázkové dotazy pro svisle dělená data, najdete [v tématu Dotazování svisle dělených dat)](sql-database-elastic-query-vertical-partitioning.md)
* Kurz horizontálního dělení (horizontálního dělení) naleznete [v tématu Začínáme s elastickým dotazem pro horizontální dělení (horizontální dělení)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělená data naleznete v [tématu Dotazování horizontálně dělených dat)](sql-database-elastic-query-horizontal-partitioning.md)
* Viz [\_sp \_spustit vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provede příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly v horizontálním dělení schéma.
