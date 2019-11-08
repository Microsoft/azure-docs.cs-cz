---
title: Přidání balíčku R do Machine Learning Services (Preview)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Tento článek vysvětluje, jak nainstalovat balíček R, který už není nainstalovaný v Azure SQL Database Machine Learning Services (Preview).
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
ms.openlocfilehash: ce85f45d823df42e70af53824e175968439621d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819869"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Přidání balíčku R do Azure SQL Database Machine Learning Services (Preview)

Tento článek vysvětluje, jak přidat balíček R do Azure SQL Database Machine Learning Services (Preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalujte prostředí [R](https://www.r-project.org) a [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) do místního počítače. Jazyk R je k dispozici pro Windows, MacOS a Linux. V tomto článku se předpokládá, že používáte Windows.

- Tento článek obsahuje příklad použití [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) ke spuštění skriptu jazyka R v Azure SQL Database. Můžete spouštět skripty R pomocí jiných nástrojů pro správu databáze nebo dotazů, ale tento příklad předpokládá Azure Data Studio nebo SSMS.
   
> [!NOTE]
> Nemůžete nainstalovat balíček pomocí skriptu R s **sp_execute_external_script** v Azure Data Studio nebo SSMS. Balíčky můžete nainstalovat a odebrat jenom pomocí příkazového řádku R a RStudio, jak je popsáno v tomto článku. Po instalaci balíčku můžete získat přístup k funkcím balíčku ve skriptu R pomocí **sp_execute_external_script**.

## <a name="list-r-packages"></a>Výpis balíčků R

Microsoft poskytuje několik balíčků R, které jsou předinstalované Machine Learning Services ve službě Azure SQL Database.
Seznam nainstalovaných balíčků R můžete zobrazit spuštěním následujícího příkazu v Azure Data Studio nebo SSMS.

1. Otevřete Azure Data Studio nebo SSMS a připojte se k Azure SQL Database.

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

Výstup by měl vypadat podobně jako následující.

**Results**

![Nainstalované balíčky v jazyce R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Přidání balíčku pomocí sqlmlutils

Pokud potřebujete použít balíček, který už není v Azure SQL Database nainstalovaný, můžete ho nainstalovat pomocí [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** je balíček navržený tak, aby uživatelům usnadnil interakci s databázemi SQL (SQL Server a Azure SQL Database) a spouštěl kód r nebo Python v SQL z klienta r nebo Pythonu. V současné době je v Azure SQL Database podporovaná jenom verze R nástroje **sqlmlutils** .

V následujícím příkladu nainstalujete balíček **[Glue](https://cran.r-project.org/web/packages/glue/)** , který může formátovat a interpolovat řetězce. Pomocí těchto kroků nainstalujete **sqlmlutils** a **RODBCext** (předpoklad pro **sqlmlutils**) a přidáte **spojovací** balíček.

### <a name="install-sqlmlutils"></a>Nainstalovat **sqlmlutils**

1. Stáhněte si nejnovější soubor zip **sqlmlutils** z https://github.com/Microsoft/sqlmlutils/tree/master/R/dist do svého místního počítače. Nemusíte ho rozkomprimovat.

1. Otevřete **příkazový řádek** a spusťte následující příkazy, abyste nainstalovali **RODBCext** a **sqlmlutils** do svého místního počítače. Nahraďte úplnou cestu k **sqlmlutils** souboru zip, který jste stáhli (příklad předpokládá, že se soubor nachází ve složce Dokumenty).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Pokud se zobrazí chybová zpráva "R" není rozpoznána jako interní nebo externí příkaz, spustitelný program nebo dávkový soubor ", pravděpodobně to znamená, že cesta k souboru R. exe není obsažena ve vaší proměnné prostředí **path** ve Windows. Můžete buď přidat cestu k proměnné prostředí, nebo přejít do složky na příkazovém řádku (například `cd C:\Program Files\R\R-3.5.3\bin`) a pak příkaz zopakovat.

### <a name="add-the-package"></a>Přidat balíček

1. Otevřete RStudio a vytvořte nový soubor **skriptu R** . 

1. Pomocí následujícího kódu R nainstalujte balíček **Glue** pomocí **sqlmlutils**. Nahraďte vlastní informace o Azure SQL Database připojení.

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
    > **Obor** může být buď **veřejný** , nebo **soukromý**. Veřejný obor je užitečný pro správce databáze, kterému umožňuje instalovat balíčky, které můžou používat všichni uživatelé. Soukromý rozsah zpřístupňuje balíček pouze uživateli, který ho nainstaluje. Pokud obor nezadáte, výchozí obor bude **SOUKROMÝ**.

### <a name="verify-the-package"></a>Ověření balíčku

Spuštěním následujícího skriptu jazyka R v RStudio ověřte, zda byl balíček pro **připevňování** nainstalován. Použijte stejné **připojení** , které jste definovali v předchozím kroku.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Obsah tabulky RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Použití balíčku

Po instalaci balíčku ho můžete použít ve skriptu R prostřednictvím **sp_execute_external_script**.

1. Otevřete Azure Data Studio nebo SSMS a připojte se k Azure SQL Database.

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

    Na kartě **zprávy** se zobrazí následující výsledek.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Odebrat balíček

Pokud chcete balíček odebrat, spusťte v RStudio následující skript R. Použijte stejné **připojení** , které jste definovali dříve.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Dalším způsobem instalace balíčku R do vaší databáze SQL Azure je nahrání balíčku R z bajtového datového proudu pomocí příkazu **Create External Library** jazyka T-SQL. Další informace najdete v tématu [Vytvoření knihovny z datového proudu bajtů](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) v referenční dokumentaci k [vytvoření externí knihovny](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) .

## <a name="next-steps"></a>Další kroky

Další informace o Azure SQL Database Machine Learning Services s R (Preview) najdete v následujících článcích.

- [Azure SQL Database Machine Learning Services s R (Preview)](sql-database-machine-learning-services-overview.md)
- [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (Preview)](sql-database-machine-learning-services-data-issues.md)