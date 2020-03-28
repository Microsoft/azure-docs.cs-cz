---
title: Přidání balíčku R do služby Machine Learning Services (preview)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Tento článek vysvětluje, jak nainstalovat balíček R, který ještě není nainstalovaný ve službě Azure SQL Database Machine Learning Services (preview).
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73819869"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Přidání balíčku R do služby Azure SQL Database Machine Learning Services (preview)

Tento článek vysvětluje, jak přidat balíček R do služby Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalujte [plochu R](https://www.r-project.org) a [RStudio](https://www.rstudio.com/products/rstudio/download/) do místního počítače. Jazyk R je k dispozici pro Windows, MacOS a Linux. Tento článek předpokládá, že používáte systém Windows.

- Tento článek obsahuje příklad použití [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) ke spuštění skriptu R v Azure SQL Database. Skripty R můžete spustit pomocí jiných nástrojů pro správu databáze nebo dotazů, ale tento příklad předpokládá Azure Data Studio nebo SSMS.
   
> [!NOTE]
> Balíček nelze nainstalovat spuštěním skriptu R pomocí **sp_execute_external_script** v Azure Data Studio nebo SSMS. Balíčky lze nainstalovat a odebrat pouze pomocí příkazového řádku R a služby RStudio, jak je popsáno v tomto článku. Po instalaci balíčku můžete přistupovat k funkcím balíčku ve skriptu R pomocí **sp_execute_external_script**.

## <a name="list-r-packages"></a>Výpis balíčků R

Společnost Microsoft poskytuje řadu balíčků R předinstalovaných se službami Machine Learning Services v databázi Azure SQL.
Seznam nainstalovaných balíčků R můžete zobrazit spuštěním následujícího příkazu v Azure Data Studio nebo SSMS.

1. Otevřete Azure Data Studio nebo SSMS a připojte se k azure sql database.

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

## <a name="add-a-package-with-sqlmlutils"></a>Přidání balíčku s sqlmlutils

Pokud potřebujete použít balíček, který ještě není nainstalovaný v azure sql database, můžete jej nainstalovat pomocí [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** je balíček určený k tomu, aby uživatelům pomohl komunikovat s databázemi SQL (SQL Server a Azure SQL Database) a spouštět kód R nebo Python v SQL z klienta R nebo Pythonu. V současné době je v Azure SQL Database podporovaná jenom verze R **sqlmlutils.**

V následujícím příkladu nainstalujete balíček **[lepidla,](https://cran.r-project.org/web/packages/glue/)** který může formátovat a interpolovat řetězce. Tyto kroky nainstalují **sqlmlutils** a **RODBCext** (předpoklad pro **sqlmlutils**) a přidají balíček **lepidla.**

### <a name="install-sqlmlutils"></a>Instalace **sqlmlutils**

1. Stáhněte si nejnovější soubor zip https://github.com/Microsoft/sqlmlutils/tree/master/R/dist **sqlmlutils** z místního počítače. Není nutné rozbalit soubor.

1. Otevřete **příkazový řádek** a spusťte následující příkazy pro instalaci **položek RODBCext** a **sqlmlutils** do místního počítače. Nahraďte úplnou cestu ke staženému souboru **SQLmlutils** zip (příklad předpokládá, že soubor je ve složce Dokumenty).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Výstup, který vidíte, by měl být podobný následujícímu.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Pokud se zobrazí chyba " 'R' není rozpoznán jako interní nebo externí příkaz, funkční program nebo dávkový soubor", pravděpodobně to znamená, že cesta k programu R.exe není zahrnuta v proměnné prostředí **PATH** v systému Windows. Můžete buď přidat cestu do proměnné prostředí, nebo přejít do `cd C:\Program Files\R\R-3.5.3\bin`složky v příkazovém řádku (například) a potom příkaz opakovat.

### <a name="add-the-package"></a>Přidání balíčku

1. Otevřete RStudio a vytvořte nový soubor **skriptu R**. 

1. Pomocí následujícího kódu R nainstalujte balíček **lepidla** pomocí **sqlmlutils**. Nahraďte vlastní informace o připojení k Azure SQL Database.

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
    > **Obor** může být **veřejné** nebo **soukromé**. Veřejný obor je užitečný pro správce databáze, kterému umožňuje instalovat balíčky, které můžou používat všichni uživatelé. Soukromý obor zpřístupní balíček pouze uživateli, který jej nainstaluje. Pokud obor nezadáte, výchozí obor bude **SOUKROMÝ**.

### <a name="verify-the-package"></a>Ověření balíčku

Ověřte, zda byl balíček **lepidla** nainstalován spuštěním následujícího skriptu R v rstudiu. Použijte stejné **připojení,** které jste definovali v předchozím kroku.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Obsah tabulky RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Použití balíčku

Po instalaci balíčku jej můžete použít ve skriptu R prostřednictvím **sp_execute_external_script**.

1. Otevřete Azure Data Studio nebo SSMS a připojte se k azure sql database.

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

    Na kartě **Zprávy** se zobrazí následující výsledek.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Odebrání balíčku

Pokud chcete balíček odebrat, spusťte v RStudiu následující skript R. Použijte stejné **připojení,** které jste definovali dříve.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Dalším způsobem, jak nainstalovat balíček R do databáze Azure SQL, je nahrát balíček R z datového proudu bajtů pomocí příkazu **CREATE EXTERNAL LIBRARY** T-SQL. Viz [Vytvoření knihovny z bajtového datového proudu](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) v referenční dokumentaci VYTVOŘIT EXTERNÍ [KNIHOVNU.](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)

## <a name="next-steps"></a>Další kroky

Další informace o službách Azure SQL Database Machine Learning Services s R (preview) najdete v následujících článcích.

- [Služby strojového učení azure SQL database s r (preview)](sql-database-machine-learning-services-overview.md)
- [Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL ve službě Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)