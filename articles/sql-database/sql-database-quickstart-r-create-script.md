---
title: Vytvořit a spustit jednoduché skripty jazyka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Spusťte jednoduché skripty jazyka R v Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013251"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Vytvořit a spustit jednoduché skripty jazyka R v Azure SQL Database Machine Learning Services (preview)

V tomto rychlém startu vytvoříte a spustit sadu jednoduché skripty R používat verzi public preview [Machine Learning Services (s jazykem R) ve službě Azure SQL Database](sql-database-machine-learning-services-overview.md). Dozvíte se víc o zabalení ve správném formátu skript jazyka R v uložené proceduře [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) a spusťte tento skript v databázi SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

- Chcete-li spustit ukázkový kód v těchto cvičeních, musíte nejprve mít databázi Azure SQL Machine Learning Services (R) povolena. Ve verzi public preview, společnost Microsoft bude připojit je a povolit strojového učení pro existující nebo nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spustit skripty R používat správu jiných databází nebo nástrojů pro dotazování, ale v tomto rychlém startu budete používat SSMS.

- Tento rychlý start vyžaduje nakonfigurovat pravidlo brány firewall na úrovni serveru. Informace o tom, jak to provést, najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Spustit jednoduchý skript

Chcete-li spustit skript R, jeho budete předáním jako argument systémové uložené procedury, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

V následujících krocích spustíte tento skript příklad R ve službě SQL database:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomoc s připojením, přečtěte si [rychlý start: Pomocí SQL Server Management Studio k připojení a dotazování Azure SQL database](sql-database-connect-query-ssms.md).

1. Předat kompletní skript R, abyste [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uložené procedury.

   Skript se předává `@script` argument. Všechno, co je uvnitř `@script` argument musí být platný kód R.

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

   Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Zobrazit [požadavky](#prerequisites) výše.

   > [!NOTE]
   > Pokud jste správce, můžete spustit externí kód automaticky. Oprávnění lze udělit ostatním uživatelům pomocí příkazu:
   <br>**UDĚLENÍ spustit libovolný externí skript do**  *\<uživatelské jméno\>*.

2. Správný výsledek se počítá a R `print` funkce vrátí výsledek, který má **zprávy** okna.

   by měl vypadat asi takhle nějak.

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Spusťte skript Hello World

Skript Typickým příkladem je ten, který právě vypíše řetězec "Hello World". Spusťte následující příkaz.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Vstupy pro tuto uloženou proceduru patří:

| | |
|-|-|
|*@language* | definuje rozšíření jazyka v tomto případě volat R |
|*@script* | Definuje předány modulem runtime r. příkazy. Celý skript R musí být uzavřena v tento argument, jako text v kódu Unicode. Můžete také přidat text do proměnné typu **nvarchar** a následně zavolat proměnné |
|*@input_data_1* | data vrácená dotazem, předán modulu runtime jazyka R, který vrací data do SQL serveru jako datový rámec |
|POMOCÍ SAD VÝSLEDKŮ DOTAZU | klauzule definuje schéma tabulky vrácená data pro SQL Server, přidání "Hello World" jako název sloupce **int** pro datový typ |

Tento příkaz zobrazí následující text:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Používat vstupy a výstupy

Ve výchozím nastavení [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) jedna datová sada přijímá jako vstup, který obvykle poskytují ve formě platný dotaz SQL. Pak vrátí jeden snímek dat R jako výstup.

Jako parametr je možné předat pouze jednu vstupní datovou sadu. Zároveň je možné vrátit pouze jednu datovou sadu. V kódu R však můžete volat i jiné datové sady a kromě datové sady vracet také výstupy jiných typů. K jakémukoli parametrů také můžete přidat klíčové slovo OUTPUT, aby se vrátil s výsledky.

Teď můžeme použít výchozí vstupní a výstupní proměnné [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** a **OutputDataSet**.

1. Vytvoří malý testovací data.

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

1. Použití `SELECT` příkaz dotaz tabulku.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Obsah tabulky RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

1. Spusťte následující skript jazyka R. Načte data z tabulky pomocí `SELECT` prohlášení, prochází modulem runtime r. a vrátí data jako datový rámec. `WITH RESULT SETS` Klauzule definuje schéma tabulky vrácená data pro SQL Database a přichází s názvem sloupce *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Výstup skriptu R, který vrací data z tabulky](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Teď Změníme názvy vstupní a výstupní proměnné. Výchozí vstupní a výstupní názvy proměnných jsou **InputDataSet** a **OutputDataSet**, tento skript změní názvy k **SQL_in** a **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Všimněte si, že R je velká a malá písmena. Vstupní a výstupní proměnné používané ve skriptu R (**SQL_out**, **SQL_in**) musí odpovídat hodnoty definované pomocí `@input_data_1_name` a `@output_data_1_name`, včetně případu.

   > [!TIP]
   > Jako parametr je možné předat pouze jednu vstupní datovou sadu. Zároveň je možné vrátit pouze jednu datovou sadu. V kódu R však můžete volat i jiné datové sady a kromě datové sady vracet také výstupy jiných typů. K jakémukoli parametrů také můžete přidat klíčové slovo OUTPUT, aby se vrátil s výsledky.

1. Také může generovat jenom pomocí skriptu R bez vstupních dat hodnoty (`@input_data_1` je nastavena na prázdnou hodnotu).

   Následující skript vypíše textu "hello" a "world".

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

    ![Výsledky dotazu s použitím proměnné @script jako vstupu](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrola verze jazyka R

Pokud chcete zjistit, kterou verzi R je nainstalována ve službě SQL database, spusťte následující skript.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Funkce R `print` vrátí verzi do okna **Zprávy**. Následující ukázkový výstup uvidíte, SQL Database v tomto případě má R 3.4.4 nainstalované verze.

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

Pokud chcete zobrazit seznam jazyka R, které jsou nainstalované balíčky, včetně verze, závislosti, licence a informace o cestě knihovny, spusťte následující skript.

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

Výstup je z `installed.packages()` v R a vrátí se v důsledku nastavení.

**Results**

![Nainstalované balíčky v jazyce R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>Další postup

Chcete-li vytvořit model strojového učení pomocí jazyka R ve službě SQL Database, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Vytvoření a natrénujeme prediktivní model v R s Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-train-score-model.md)

Další informace o Machine Learning Services najdete v níže uvedených článků. I když některé z těchto článků pro SQL Server, většina informace platí také pro Machine Learning Services (s jazykem R) ve službě Azure SQL Database.

- [Azure SQL Database služby Machine Learning (s jazykem R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Kurz: Zjistěte, analýza v databázi pomocí jazyka R na SQL serveru](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Ucelený průvodce datovými vědami pro jazyk R a SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Kurz: Funkce RevoScaleR R s daty formátu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
