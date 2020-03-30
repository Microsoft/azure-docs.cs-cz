---
title: Použití R se službami strojového učení k dotazování na databázi (preview)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Tento článek ukazuje, jak pomocí skriptu R se službami Azure SQL Database Machine Learning Services připojit k databázi Azure SQL a dotazovat se na něj pomocí příkazů Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768517"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Úvodní příručka: Použití r se službami strojového učení k dotazování na databázi Azure SQL (preview)

V tomto rychlém startu použijete R se službami strojového učení k připojení k databázi Azure SQL a pomocí příkazů T-SQL k dotazování na data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze Azure SQL](sql-database-single-database-get-started.md)
- [Služby strojového učení](sql-database-machine-learning-services-overview.md) s povolenou r. [Zaregistrovat verzi Preview](sql-database-machine-learning-services-overview.md#signup)
- [Sql Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skripty v tomto článku jsou zapsány k použití databáze **Adventure Works.**

> [!NOTE]
> Během verze Public Preview vás Společnost Microsoft zavede do sítě a povolí strojové učení pro vaši stávající nebo novou databázi, ale možnost nasazení spravované instance není momentálně podporována.

Machine Learning Services s R je funkce databáze Azure SQL, která se používá pro spouštění skriptů R v databázi. Další informace naleznete v [projektu R](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.**

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. Otevřete **SQL Server Management Studio** a připojte se k databázi SQL.

   Pokud potřebujete pomoc s připojením, přečtěte si [úvodní příručku: Pomocí aplikace SQL Server Management Studio se můžete připojit k databázi Azure SQL a zadat dotaz](sql-database-connect-query-ssms.md)na ni .

1. Předajte úplný skript R [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uložené procedury.

   Skript je předán `@script` prostřednictvím argumentu. Vše uvnitř `@script` argumentu musí být platný kód R.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která si můžete vybrat jako zdroj při vytváření databáze. Pokud databáze obsahuje jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Viz [Požadavky](#prerequisites) výše.

## <a name="run-the-code"></a>Spuštění kódu

1. Proveďte [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uloženou proceduru.

1. Ověřte, zda jsou v okně **Zprávy** vráceny 20 řádků kategorie/produktu.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)
- [Služby strojového učení azure SQL database (s R)](sql-database-machine-learning-services-overview.md)
- [Vytváření a spouštění jednoduchých skriptů R ve službách Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)
- [Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
