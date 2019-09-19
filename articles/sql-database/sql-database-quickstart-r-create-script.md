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
ms.openlocfilehash: a47e7a81ba486056841bdc0fe65cfd10f1b2c412
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123194"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Vytvoření a spuštění jednoduchých skriptů R v Azure SQL Database Machine Learning Services (Preview)

V tomto rychlém startu vytvoříte a spustíte sadu jednoduchých skriptů R pomocí veřejné verze Preview [Machine Learning Services (s R) v Azure SQL Database](sql-database-machine-learning-services-overview.md). Naučíte se, jak zabalit dobře vytvořený skript R do uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) a spustit skript v databázi SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Chcete-li spustit vzorový kód v těchto cvičeních, musíte nejprve mít službu Azure SQL Database s povoleným Machine Learning Services (s R). V rámci verze Public Preview vám Microsoft zaregistruje a povolí Machine Learning pro vaši stávající nebo novou databázi. Postupujte podle kroků v [části registrace ve verzi Preview](sql-database-machine-learning-services-overview.md#signup).

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spouštět skripty R pomocí jiných nástrojů pro správu databáze nebo dotazů, ale v tomto rychlém startu použijete SSMS.

- Tento rychlý Start vyžaduje, abyste nakonfigurovali pravidlo brány firewall na úrovni serveru. Informace o tom, jak to provést, najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Spuštění jednoduchého skriptu

Pokud chcete spustit skript jazyka R, předáte ho jako argument do systémové uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

V následujících krocích spustíte v SQL Database tento příklad skriptu jazyka R:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomáhat s připojením, přečtěte si [rychlý Start: Pomocí SQL Server Management Studio se můžete připojit k databázi](sql-database-connect-query-ssms.md)SQL Azure a dotazovat se na ně.

1. Předejte kompletní skript R do uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skript se předává přes `@script` argument. Vše uvnitř `@script` argumentu musí být platný kód R.

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
   <br>**udělit všem externím skriptům spuštění** *uživatelskéjméno\>. \<*

2. Je vypočítán správný výsledek a funkce R `print` vrátí výsledek do okna **zprávy** .

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
| 1 |

## <a name="use-inputs-and-outputs"></a>Použití vstupů a výstupů

Ve výchozím nastavení [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) přijímá jedinou datovou sadu jako vstup, což obvykle poskytuje formu platného dotazu SQL. Pak vrátí jeden datový rámec R jako výstup.

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

1. `SELECT` Použijte příkaz pro dotazování tabulky.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Obsah tabulky RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Spusťte následující skript jazyka R. Načte data z tabulky pomocí `SELECT` příkazu, předává je pomocí modulu runtime R a vrátí data jako datový rámec. Klauzule definuje schéma tabulky vrácených dat pro SQL Database přidáním názvu sloupce *NewColName.* `WITH RESULT SETS`

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

    Všimněte si, že R rozlišuje velká a malá písmena. Vstupní a výstupní proměnné, které se používají ve skriptu jazyka R (**SQL_out**, **SQL_in**), musí odpovídat hodnotám `@input_data_1_name` definovaným s a `@output_data_1_name`, včetně případu.

   > [!TIP]
   > Jako parametr je možné předat pouze jednu vstupní datovou sadu. Zároveň je možné vrátit pouze jednu datovou sadu. V kódu R však můžete volat i jiné datové sady a kromě datové sady vracet také výstupy jiných typů. K jakémukoli parametrů také můžete přidat klíčové slovo OUTPUT, aby se vrátil s výsledky.

1. Můžete také generovat hodnoty jenom pomocí skriptu R bez vstupních dat (`@input_data_1` je nastavené na prázdné).

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

Výstup je z `installed.packages()` R a vrátí se jako sada výsledků.

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
