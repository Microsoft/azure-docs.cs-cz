---
title: Přidat balíček R do Azure SQL Database Machine Learning Services (preview)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Tento článek vysvětluje, jak nainstalovat balíček R, který ještě není nainstalovaná v Azure SQL Database Machine Learning Services (preview).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927097"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Přidat balíček R do Azure SQL Database Machine Learning Services (preview)

Tento článek vysvětluje, jak přidat balíček R do Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [R](https://www.r-project.org) a [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) v místním počítači. Jazyk R je k dispozici pro Windows, MacOS a Linux. Tento článek předpokládá, že používáte Windows.

- Tento článek obsahuje příklad použití [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) skriptu (SSMS) ke spuštění R ve službě Azure SQL Database. Můžete spustit skripty R používat správu jiných databází nebo nástrojů pro dotazování, ale tento příklad předpokládá Azure Data Studio nebo aplikace SSMS.
   
> [!NOTE]
> Balíček nelze nainstalovat spuštěním skriptu R pomocí **sp_execute_external_script** v Azure Data studiu nebo v aplikaci SSMS. Můžete nainstalovat a odebrání balíčků pomocí příkazového řádku jazyka R a nástroje RStudio, jak je popsáno v tomto článku. Jakmile je balíček nainstalován, dostanete balíček funkcí ve skriptu R pomocí **sp_execute_external_script**.

## <a name="list-r-packages"></a>Výpis balíčků R

Společnost Microsoft poskytuje počet balíčků R s předinstalovanou služby Machine Learning v Azure SQL database.
Zobrazí se seznam nainstalovaných balíčků R spuštěním následujícího příkazu v Azure Data studiu nebo v aplikaci SSMS.

1. Otevřete Azure Data Studio nebo aplikace SSMS a připojte se k Azure SQL Database.

1. Spusťte následující příkaz:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Výstup by měl vypadat nějak takto.

**Results**

![Nainstalované balíčky v jazyce R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Přidat balíček s sqlmlutils

Pokud je potřeba použít balíček, který ještě není nainstalovaná ve službě Azure SQL Database, můžete nainstalovat pomocí [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** je balíček, který je navržený tak, aby pomáhal uživatelům interakci s databází SQL (SQL Server a Azure SQL Database) a spustit kód R nebo Python ve službě SQL z klienta R nebo Python. V současné době pouze R verzi **sqlmlutils** se podporuje ve službě Azure SQL Database.

V následujícím příkladu nainstalujete **[připevnit](https://cran.r-project.org/web/packages/glue/)** balíček, který můžete naformátovat a interpolovat řetězce. Postup instalace **sqlmlutils** a **RODBCext** (předpokladem pro **sqlmlutils**) a přidejte **připevnit** balíčku.

### <a name="install-sqlmlutils"></a>Install **sqlmlutils**

1. Stáhněte si nejnovější **sqlmlutils** soubor zip z https://github.com/Microsoft/sqlmlutils/tree/master/R/dist do místního počítače. Není nutné dekomprimovat soubor.

1. Otevřít **příkazového řádku** a spusťte následující příkazy, abyste nainstalovali **RODBCext** a **sqlmlutils** v místním počítači. Nahraďte úplnou cestu k **sqlmlutils** souboru zip staženého (příklad předpokládá, je soubor ve složce Dokumenty).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Který se zobrazí výstup by měl vypadat přibližně takto.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Pokud se zobrazí chyba, "" R"nebyl rozpoznán jako vnitřního ani vnějšího příkazu, spustitelného programu nebo dávkového souboru", pravděpodobně znamená, že cesta k R.exe není součástí vaší **cesta** proměnné prostředí na Windows. Můžete přidat cestu do proměnné prostředí nebo přejděte do složky na příkazovém řádku (například `cd C:\Program Files\R\R-3.5.3\bin`) a pak opakujte příkaz.

### <a name="add-the-package"></a>Přidání balíčku

1. Otevřete RStudio a vytvořte nový **skript jazyka R** souboru. 

1. Pomocí následujícího kódu jazyka R k instalaci **připevnit** balíček pomocí **sqlmlutils**. Nahraďte svými vlastními informacemi připojení Azure SQL Database.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **Oboru** může být buď **veřejné** nebo **PRIVÁTNÍ**. Veřejný obor je užitečný pro správce databáze, kterému umožňuje instalovat balíčky, které můžou používat všichni uživatelé. Soukromý obor zpřístupňuje balíček pouze na uživatele, který ho nainstaluje. Pokud obor nezadáte, výchozí obor bude **SOUKROMÝ**.

### <a name="verify-the-package"></a>Ověření balíčku

Ověřte, že **připevnit** balíček nainstaloval spuštěním následujícího skriptu R v RStudio. Použijte stejný **připojení** jste definovali v předchozím kroku.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Obsah tabulky RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Pomocí balíčku

Po instalaci balíčku, můžete ho použít ve skriptu R pomocí **sp_execute_external_script**.

1. Otevřete Azure Data Studio nebo aplikace SSMS a připojte se k Azure SQL Database.

1. Spusťte následující příkaz:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    Uvidíte následující výsledek **zprávy** kartu.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Odeberte balíček

Pokud chcete daný balíček odebrat, spusťte následující skript jazyka R v RStudio. Použijte stejný **připojení** jste definovali dříve.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Dalším způsobem, jak nainstalovat balíčky R k vaší databázi Azure SQL je pro nahrání balíčku R pomocí datového proudu bajtů **CREATE EXTERNAL LIBRARY** příkazu T-SQL. Zobrazit [vytvořit knihovnu z datového proudu bajtů](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) v [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) referenční dokumentaci.

## <a name="next-steps"></a>Další postup

Další informace o Azure SQL Database služby Machine Learning s jazykem R (preview) naleznete v následujících článcích.

- [Azure SQL Database služby Machine Learning s jazykem R (preview)](sql-database-machine-learning-services-overview.md)
- [Zápis pokročilé funkce jazyka R ve službě Azure SQL Database pomocí služby Machine Learning (preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)