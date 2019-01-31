---
title: Začínáme s mezidatabázovými dotazy (vertikální oddíly) | Dokumentace Microsoftu
description: jak používat dotaz na elastickou databázi s vertikálně dělenou databází
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 116a465a0ddc913e342e0ffcc1fb29f5bf969419
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464156"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Začínáme s mezidatabázovými dotazy (vertikální oddíly) (preview)

Dotaz na elastickou databázi (preview) pro službu Azure SQL Database umožňuje spustit dotazy T-SQL, které zahrnují více databází pomocí jednoho připojení bodu. Tento článek se týká [vertikálně dělené databáze](sql-database-elastic-query-vertical-partitioning.md).  

Po dokončení bude: Zjistěte, jak nakonfigurovat a používat službu Azure SQL Database můžete provádět dotazy, které jsou rozmístěny v několika souvisejících databází.

Další informace o funkci elastické databáze dotazů, najdete v části [přehled dotazů Azure SQL Database elastic database](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Požadavky

Je vyžadováno oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE. K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.

## <a name="create-the-sample-databases"></a>Vytvoření ukázkové databáze

Začněte tím, vytvořte dvě databáze, **zákazníkům** a **objednávky**, buď v jednom nebo několika servery SQL Database.

Spusťte následující dotazy na **objednávky** databázi vytvořte **OrderInformation** tabulky a vstupní ukázková data.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Nyní, spusťte následující dotaz na **zákazníkům** databázi vytvořte **CustomerInformation** tabulky a vstupní ukázková data.

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

### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč a přihlašovací údaje v oboru databáze

1. Otevřete SQL Server Management Studio nebo SQL Server Data Tools v sadě Visual Studio.
2. Připojení k databázi objednávek a spusťte následující příkazy T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    Uživatelské jméno a heslo slouží k přihlašování do databáze zákazníků, by měl být "username" a "password".
    Ověřování pomocí Azure Active Directory s elastickými dotazy se momentálně nepodporuje.

### <a name="external-data-sources"></a>Externí zdroje dat

K vytvoření externího zdroje dat, spusťte následující příkaz v databázi objednávek:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Externí tabulky

Vytvoření externí tabulky v databázi objednávek, které by odpovídalo definici tabulky CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového dotazu T-SQL elastické databáze

Po definování externího zdroje dat. a externích tabulek, teď můžete T-SQL k dotazování externí tabulky. Spusťte tento dotaz na databázi objednávek:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Náklady

V současné době funkce dotazu elastických databází je zahrnutá do ceny za Azure SQL Database.  

Informace o cenách najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Další postup

* Přehled elastický dotaz, naleznete v tématu [elastický dotaz přehled](sql-database-elastic-query-overview.md).
* Syntaxe a ukázkové dotazy na vertikálně dělená data, najdete v části [dotazování na vertikálně dělené data)](sql-database-elastic-query-vertical-partitioning.md)
* Horizontální dělení (sharding) kurz najdete v tématu [Začínáme se službou elastický dotaz pro horizontální dělení (sharding)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělená data, najdete v části [dotazování na horizontálně dělené data)](sql-database-elastic-query-horizontal-partitioning.md)
* Naleznete v tématu [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provádí příkaz jazyka Transact-SQL na jeden vzdálený Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly takovým vodorovné schéma vytváření oddílů.
