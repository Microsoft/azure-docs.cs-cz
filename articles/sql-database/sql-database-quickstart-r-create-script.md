---
title: Vytváření a spouštění jednoduchých skriptů R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Spouštějte jednoduché skripty R v Azure SQL Database Machine Learning Services (Preview).
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
ms.openlocfilehash: 4db4bf7c9d35d9715f24ac0480abfaad7eb7c9e6
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760075"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Rychlý Start: vytvoření a spuštění jednoduchých skriptů R v Azure SQL Database Machine Learning Services (Preview)

V tomto rychlém startu vytvoříte a spustíte sadu skriptů R pomocí Machine Learning Services (s R) v Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [Azure SQL Database](sql-database-single-database-get-started.md) s [pravidlem brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md)

- [Machine Learning Services](sql-database-machine-learning-services-overview.md) s povoleným R. [Zaregistrujte se do verze Preview](sql-database-machine-learning-services-overview.md#signup).

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> V rámci verze Public Preview vám Microsoft zaregistruje a povolí Machine Learning pro vaši stávající nebo novou databázi.

V tomto příkladu se používá uložená procedura [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) k zabalení dobře formátovaného skriptu jazyka R.

## <a name="run-a-simple-script"></a>Spuštění jednoduchého skriptu

Pokud chcete spustit skript jazyka R, předáte ho jako argument do systémové uložené procedury, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

V následujících krocích spustíte v SQL Database tento příklad skriptu jazyka R:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomáhat s připojením, přečtěte si téma [rychlý Start: použití SQL Server Management Studio k připojení a dotazování databáze SQL Azure](sql-database-connect-query-ssms.md).

1. Předejte kompletní skript R do uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skript se předává pomocí argumentu `@script`. Vše uvnitř argumentu `@script` musí být platný kód R.

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

   Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Viz [předpoklady](#prerequisites) výše.

   > [!NOTE]
   > Pokud jste správce, můžete externí kód spustit automaticky. Oprávnění ostatním uživatelům můžete udělit pomocí příkazu:
   <br>Udělte *\<uživatelského jména\>* **spuštěním libovolného externího skriptu** .

2. Je vypočítán správný výsledek a funkce `print` R vrátí výsledek do okna **zprávy** .

   mělo by to vypadat nějak takto.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Spuštění skriptu Hello World

Typický ukázkový skript je ten, který pouze vypíše řetězec "Hello World". Spusťte následující příkaz.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Vstupy do této uložené procedury zahrnují:

| | |
|-|-|
| @language | definuje rozšíření jazyka, které se má volat, v tomto případě R. |
| @script | definuje příkazy předané do modulu runtime jazyka R. Do tohoto argumentu musí být uzavřený celý skript jazyka R jako text v kódu Unicode. Můžete také přidat text do proměnné typu **nvarchar** a pak zavolat proměnnou |
| @input_data_1 | data vrácená dotazem, která se předávají do modulu runtime jazyka R, který vrací data, která se SQL Server jako datový rámec |
|S VYUŽITÍM SAD VÝSLEDKŮ | klauzule definuje schéma tabulky vrácených dat pro SQL Server a jako název sloupce přidá "Hello World" a jako název datového typu **int** . |

Příkaz vypíše následující text:

| Hello World |
|-------------|
| 1\. místo |

## <a name="use-inputs-and-outputs"></a>Použití vstupů a výstupů

Ve výchozím nastavení [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) přijímá jako vstup jedinou datovou sadu, která obvykle zadáváte ve formě platného dotazu SQL. Pak vrátí jeden datový rámec R jako výstup.

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

1. K dotazování tabulky použijte příkaz `SELECT`.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Obsah tabulky RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Spusťte následující skript jazyka R. Načítá data z tabulky pomocí příkazu `SELECT`, projde je pomocí modulu runtime R a vrátí data jako datový rámec. Klauzule `WITH RESULT SETS` definuje schéma tabulky vrácených dat pro SQL Database přidáním názvu sloupce *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Výstup skriptu R, který vrací data z tabulky](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Teď změníte názvy vstupních a výstupních proměnných. Výchozí vstupní a výstupní názvy proměnných jsou **InputDataSet** a **OutputDataSet**. Tento skript změní názvy na **SQL_in** a **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Všimněte si, že R rozlišuje velká a malá písmena. Vstupní a výstupní proměnné použité ve skriptu R (**SQL_out**, **SQL_in**) musí odpovídat hodnotám definovaným s `@input_data_1_name` a `@output_data_1_name`, včetně Case.

   > [!TIP]
   > Jako parametr je možné předat pouze jednu vstupní datovou sadu. Zároveň je možné vrátit pouze jednu datovou sadu. V kódu R však můžete volat i jiné datové sady a kromě datové sady vracet také výstupy jiných typů. K jakémukoli parametrů také můžete přidat klíčové slovo OUTPUT, aby se vrátil s výsledky.

1. Můžete taky generovat hodnoty jenom pomocí skriptu R bez vstupních dat (`@input_data_1` je nastavené na prázdné).

   Následující skript vypíše text "Hello" a "World".

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

Pokud chcete zjistit, která verze jazyka R je nainstalována ve vaší databázi SQL, spusťte následující skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Funkce R `print` vrátí verzi do okna **Zprávy**. Ve výstupu níže vidíte, že SQL Database v tomto případě je nainstalována aplikace R verze 3.4.4.

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

Chcete-li zobrazit seznam, které balíčky jazyka R jsou nainstalovány, včetně verze, závislostí, licence a informací o cestě ke knihovně, spusťte následující skript.

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

Výstup je z `installed.packages()` v R a je vrácen jako sada výsledků.

**Results**

![Nainstalované balíčky v jazyce R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit model strojového učení pomocí R v SQL Database, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Vytvoření a výuka prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)](sql-database-quickstart-r-train-score-model.md)

Další informace o Azure SQL Database Machine Learning Services s R (Preview) najdete v následujících článcích.

- [Azure SQL Database Machine Learning Services s R (Preview)](sql-database-machine-learning-services-overview.md)
- [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (Preview)](sql-database-machine-learning-services-data-issues.md)
