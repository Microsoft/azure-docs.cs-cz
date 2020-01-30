---
title: Použití R s Machine Learning Services k dotazování databáze (Preview)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V tomto článku se dozvíte, jak použít skript R s Azure SQL Database Machine Learning Services pro připojení k databázi SQL Azure a dotazování s použitím příkazů jazyka Transact-SQL.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768517"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Rychlý Start: použití R s Machine Learning Services k dotazování databáze SQL Azure (Preview)

V tomto rychlém startu použijete R se Machine Learning Services pro připojení k databázi SQL Azure a použití příkazů T-SQL k dotazování dat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze SQL Azure](sql-database-single-database-get-started.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) s povoleným R. [Zaregistrujte se do verze Preview](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

> [!NOTE]
> V rámci verze Public Preview vám Microsoft zaregistruje a povolí Machine Learning pro stávající nebo novou databázi, ale možnost nasazení Managed instance se momentálně nepodporuje.

Machine Learning Services s R je funkce služby Azure SQL Database, která se používá ke spouštění skriptů jazyka R v databázi. Další informace naleznete v projektu jazyka [R](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomáhat s připojením, přečtěte si téma [rychlý Start: použití SQL Server Management Studio k připojení a dotazování databáze SQL Azure](sql-database-connect-query-ssms.md).

1. Předejte kompletní skript R do uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skript se předává pomocí argumentu `@script`. Vše uvnitř argumentu `@script` musí být platný kód R.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Viz [předpoklady](#prerequisites) výše.

## <a name="run-the-code"></a>Spuštění kódu

1. Spusťte uloženou proceduru [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

1. Ověřte, zda je v okně **zprávy** vráceno prvních 20 řádků kategorie/produktu.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (s R)](sql-database-machine-learning-services-overview.md)
- [Vytvoření a spuštění jednoduchých skriptů R v Azure SQL Database Machine Learning Services (Preview)](sql-database-quickstart-r-create-script.md)
- [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](sql-database-machine-learning-services-functions.md)
