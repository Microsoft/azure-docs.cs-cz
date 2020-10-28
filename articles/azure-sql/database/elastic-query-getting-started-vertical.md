---
title: Začínáme s mezidatabázovými dotazy
description: Jak používat dotaz na elastickou databázi s vertikálně rozdělenými databázemi
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: dd8a30c60c81ef2761d5dc051d2dfeb300e1bac4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792119"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Začínáme s mezidatabázovými dotazy (vertikální dělení) (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Elastický databázový dotaz (Preview) pro Azure SQL Database umožňuje spouštění dotazů T-SQL, které jsou rozloženy na více databází pomocí jediného spojovacího bodu. Tento článek se vztahuje na [vertikálně dělené databáze](elastic-query-vertical-partitioning.md).  

Po dokončení se dozvíte, jak nakonfigurovat a použít Azure SQL Database k provádění dotazů, které mají více souvisejících databází.

Další informace o funkci dotazování elastické databáze najdete v tématu  [Azure SQL Database přehled dotazů elastické databáze](elastic-query-overview.md).

## <a name="prerequisites"></a>Předpoklady

Změna všech oprávnění k EXTERNÍmu zdroji dat je povinné. Toto oprávnění je součástí oprávnění ALTER DATABASE. Aby bylo možné odkazovat na podkladový zdroj dat, je třeba změnit všechna oprávnění ke zdroji externích dat.

## <a name="create-the-sample-databases"></a>Vytvoření ukázkových databází

Pokud chcete začít, vytvořte na stejném nebo jiném serveru dvě databáze, **zákazníky** a **objednávky** .

Spusťte následující dotazy v databázi **Orders** a vytvořte tabulku **OrderInformation** a zadejte vzorová data.

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

Nyní proveďte následující dotaz na databázi **zákazníků** a vytvořte tabulku **CustomerInformation** a zadejte Ukázková data.

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>Vytváření databázových objektů

### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč a přihlašovací údaje v oboru databáze

1. Otevřete SQL Server Management Studio nebo SQL Server Data Tools v aplikaci Visual Studio.
2. Připojte se k databázi Orders a spusťte následující příkazy T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    Uživatelské jméno a heslo by mělo být uživatelské jméno a heslo, které jste použili k přihlášení do databáze Customers.
    Ověřování pomocí Azure Active Directory s elastickými dotazy není v současné době podporováno.

### <a name="external-data-sources"></a>Externí zdroje dat

Chcete-li vytvořit externí zdroj dat, spusťte následující příkaz v databázi Orders:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>Externí tabulky

Vytvořte externí tabulku v databázi Orders, která odpovídá definici tabulky CustomerInformation:

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového dotazu T-SQL pro elastickou databázi

Po definování externího zdroje dat a externích tabulek teď můžete použít T-SQL k dotazování externích tabulek. Spustit tento dotaz v databázi objednávek:

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Náklady

V současné době je funkce dotazování elastické databáze zahrnutá do nákladů na vaše Azure SQL Database.  

Informace o cenách najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v tématu [Přehled elastického dotazu](elastic-query-overview.md).
* Syntaxe a ukázkové dotazy pro vertikálně dělená data najdete v tématu [dotazování na vertikálně dělená data](elastic-query-vertical-partitioning.md) .
* Kurz horizontálního dělení na oddíly (horizontálního dělení) najdete v tématu [Začínáme s elastickým dotazem pro horizontální dělení na oddíly (horizontálního dělení)](elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně rozdělená data najdete v tématu [dotazování na horizontálně dělená data](elastic-query-horizontal-partitioning.md) .
* V tématu [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) pro uloženou proceduru, která provádí příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadě databází, která slouží jako horizontálních oddílů ve vodorovném schématu dělení.