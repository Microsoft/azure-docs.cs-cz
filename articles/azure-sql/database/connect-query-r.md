---
title: Použití R s Azure SQL Database Machine Learning Services (Preview) k dotazování databáze
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V tomto článku se dozvíte, jak pomocí skriptu R s Azure SQL Database Machine Learning Services připojit k databázi v Azure SQL Database a dotazovat ji pomocí příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2e32a4abeae78aa7105f21ecffbb18c2eae841a4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185619"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Rychlý Start: použití R s Azure SQL Database Machine Learning Services (Preview) k dotazování databáze 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto rychlém startu použijete R se Azure SQL Database Machine Learning Services pro připojení k databázi v Azure SQL Database a použití příkazů T-SQL k dotazování dat.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL Database](single-database-create-quickstart.md)
- [Machine Learning Services](../managed-instance/machine-learning-services-overview.md) s povoleným R.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

Machine Learning Services s R je funkce Azure SQL Database používaná ke spouštění skriptů jazyka R v databázi. Další informace naleznete v projektu jazyka [R](https://www.r-project.org/).

## <a name="get-the-sql-server-connection-information"></a>Získat informace o SQL Server připojení

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle pole **název serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaný název serveru vedle **hostitele** pro SPRAVOVANOU instanci ve spravované instanci Azure SQL. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="create-code-to-query-your-database"></a>Vytvoření kódu pro dotazování databáze

1. Otevřete **SQL Server Management Studio** a připojte se k databázi.

   Pokud potřebujete pomáhat s připojením, přečtěte si téma [rychlý Start: použití SQL Server Management Studio k připojení a dotazování databáze v Azure SQL Database](connect-query-ssms.md).

1. Předejte kompletní skript R do uložené procedury [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skript se předává přes `@script` argument. Vše uvnitř `@script` argumentu musí být platný kód R.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Pokud dojde k chybám, může to být proto, že verze Public Preview Machine Learning Services (s R) není pro vaši databázi povolená. Viz [předpoklady](#prerequisites) výše.

## <a name="run-the-code"></a>Spuštění kódu

1. Spusťte uloženou proceduru [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

1. Ověřte, zda je v okně **zprávy** vráceno prvních 20 řádků kategorie/produktu.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze v Azure SQL Database](design-first-database-tutorial.md)
- [Azure SQL Database Machine Learning Services (s R)](../managed-instance/machine-learning-services-overview.md)
- [Vytvoření a spuštění jednoduchých skriptů R v Azure SQL Database Machine Learning Services (Preview)](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context)