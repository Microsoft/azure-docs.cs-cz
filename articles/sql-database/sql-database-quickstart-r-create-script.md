---
title: Vytvoření a spuštění jednoduchých skriptů Jazyka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Spouštět jednoduché skripty R ve službě Azure SQL Database Machine Learning Services (preview).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 5b2f8231952d25f5858f8e06a957f1056ecc3651
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768494"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Úvodní příručka: Vytvoření a spuštění jednoduchých skriptů Jazyka R ve službách Azure SQL Database Machine Learning Services (preview)

V tomto rychlém startu vytvoříte a spustíte sadu skriptů R pomocí služby Machine Learning Services (s R) v Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze Azure SQL](sql-database-single-database-get-started.md) s [pravidlem brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md)
- [Služby strojového učení](sql-database-machine-learning-services-overview.md) s povolenou r. [Zaregistrovat verzi Preview](sql-database-machine-learning-services-overview.md#signup)
- [Sql Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Během veřejné verze Preview vás Microsoft připojí k vaší službě a umožní strojové učení pro vaši stávající nebo novou databázi.

Tento příklad používá uloženou proceduru [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) k zabalení dobře formátovaného skriptu R.

## <a name="run-a-simple-script"></a>Spuštění jednoduchého skriptu

Chcete-li spustit skript R, předáte jej jako argument systému uložené procedury, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

V následujících krocích spustíte tento ukázkový skript R v databázi SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otevřete **SQL Server Management Studio** a připojte se k databázi SQL.

   Pokud potřebujete pomoc s připojením, přečtěte si [úvodní příručku: Pomocí aplikace SQL Server Management Studio se můžete připojit k databázi Azure SQL a zadat dotaz](sql-database-connect-query-ssms.md)na ni .

1. Předajte úplný skript R [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uložené procedury.

   Skript je předán `@script` prostřednictvím argumentu. Vše uvnitř `@script` argumentu musí být platný kód R.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Viz [Požadavky](#prerequisites) výše.

   > [!NOTE]
   > Pokud jste správce, můžete externí kód spustit automaticky. Pomocí příkazu můžete udělit oprávnění ostatním uživatelům:
   <br>**GRANT PROVÉST JAKÝKOLI EXTERNÍ SKRIPT UŽIVATELSKÉ** * \<JMÉNO\>*.

2. Vypočítá se správný výsledek `print` a funkce R vrátí výsledek do okna **Zprávy.**

   Mělo by to vypadat nějak takhle.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Spuštění skriptu Hello World

Typický příklad skriptu je ten, který právě výstupy řetězec "Hello World". Spusťte následující příkaz.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Vstupy do této uložené procedury patří:

| | |
|-|-|
| @language | definuje rozšíření jazyka pro volání, v tomto případě R |
| @script | definuje příkazy předané do runtime R. Celý skript R musí být uzavřen v tomto argumentu jako text Unicode. Můžete také přidat text do proměnné typu **nvarchar** a pak volat proměnnou |
| @input_data_1 | data vrácená dotazem, předaná modulu runtime R, který vrací data serveru SQL Server jako datový rámec |
|SE SADAMI VÝSLEDKŮ | klauzule definuje schéma tabulky vrácených dat pro SQL Server přidáním "Hello World" jako název **sloupce, int** pro datový typ |

Příkaz vypíše následující text:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Použití vstupů a výstupů

Ve výchozím nastavení [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) přijímá jako vstup jednu datovou sadu, kterou obvykle zadáte ve formě platného dotazu SQL. Potom vrátí jeden datový snímek R jako výstup.

Prozatím použijeme výchozí vstupní a výstupní proměnné [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** a **OutputDataSet**.

1. Vytvořte malou tabulku testovacích dat.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Pomocí `SELECT` příkazu zadejte dotaz na tabulku.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Obsah tabulky RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Spusťte následující skript R. Načte data z tabulky pomocí `SELECT` příkazu, předá je prostřednictvím runtime R a vrátí data jako datový rámec. Klauzule `WITH RESULT SETS` definuje schéma vrácené tabulky dat pro databázi SQL a přidává název sloupce *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Výstup skriptu R, který vrací data z tabulky](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Nyní změníme názvy vstupních a výstupních proměnných. Výchozí názvy vstupních a výstupních proměnných jsou **InputDataSet** a **OutputDataSet**, tento skript změní názvy na **SQL_in** a **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Všimněte si, že R rozlišuje malá a velká písmena. Vstupní a výstupní proměnné použité ve skriptu R (**SQL_out**, `@input_data_1_name` **SQL_in**) musí odpovídat definovaným hodnotám a `@output_data_1_name`, včetně případu.

   > [!TIP]
   > Jako parametr je možné předat pouze jednu vstupní datovou sadu. Zároveň je možné vrátit pouze jednu datovou sadu. V kódu R však můžete volat i jiné datové sady a kromě datové sady vracet také výstupy jiných typů. K jakémukoli parametrů také můžete přidat klíčové slovo OUTPUT, aby se vrátil s výsledky.

1. Můžete také generovat hodnoty pouze pomocí skriptu`@input_data_1` R bez vstupních dat (je nastavena na prázdné).

   Následující skript výstupy text "hello" a "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Výsledky dotazu s použitím proměnné @script jako vstupu](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrola verze jazyka R

Pokud chcete zjistit, která verze jazyka R je nainstalována v databázi SQL, spusťte následující skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Funkce R `print` vrátí verzi do okna **Zprávy**. V příkladu výstupu níže, můžete vidět, že SQL Database v tomto případě má r verze 3.4.4 nainstalován.

**Results**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Výpis balíčků R

Microsoft poskytuje v rámci služby Machine Learning Services v databázi SQL řadu předinstalovaných balíčků R.

Chcete-li zobrazit seznam balíčků R, které jsou nainstalovány, včetně verze, závislostí, licence a informací o cestě knihovny, spusťte následující skript.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Výstup je `installed.packages()` z v R a je vrácena jako sada výsledků.

**Results**

![Nainstalované balíčky v jazyce R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Další kroky

Chcete-li vytvořit model strojového učení pomocí jazyka R v databázi SQL, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Vytvoření a trénování prediktivního modelu v R pomocí služby Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-train-score-model.md)

Další informace o službách Azure SQL Database Machine Learning Services s R (preview) najdete v následujících článcích.

- [Služby strojového učení azure SQL database s r (preview)](sql-database-machine-learning-services-overview.md)
- [Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL ve službě Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)
