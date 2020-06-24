---
title: 'Kurz: Příprava dat pro výuku prediktivního modelu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V první části této série výukových kurzů budete připravovat data z databáze v Azure SQL Database, abyste mohli vytvořit prediktivní model v R s Azure SQL Database Machine Learning Services (Preview).
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 698cc089f770d60b6399864c9832fbc8d104c16f
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253796"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Příprava dat pro výuku prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V první části této série kurzů pro tři části budete importovat a připravit data z databáze v Azure SQL Database pomocí jazyka R. Později v této sérii budete tato data používat k výuce a Nasazení prediktivního modelu strojového učení v jazyce R s Azure SQL Database Machine Learning Services (Preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

V této sérii kurzů si představte, že jste vlastníkem programu Ski půjčovna a chcete předpovědět počet zapůjčení, které budete mít k budoucímu datu. Tyto informace vám pomůžou zajistit připravenost vašich akcií, zaměstnanců a zařízení.

V rámci jedné a dvou částí této série budete vyvíjet několik skriptů R v RStudio, abyste mohli připravit vaše data a naučit model strojového učení. Pak v části 3 spustíte tyto skripty R v databázi pomocí uložených procedur.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Import ukázkové databáze do databáze v Azure SQL Database pomocí jazyka R
> * Načtení dat z databáze do datového rámce R
> * Příprava dat v jazyce R určením některých sloupců jako kategorií

V [druhé části](predictive-model-build-compare-tutorial.md)se dozvíte, jak vytvořit a naučit více modelů strojového učení v jazyce R a pak zvolit nejpřesnější model.

V [třetí části](predictive-model-deploy-tutorial.md)se dozvíte, jak uložit model do databáze a pak vytvořit uložené procedury z skriptů R, které jste vytvořili v částech One a 2. Uložené procedury se spustí v databázi, aby se předpovědi vytvářely na základě nových dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/) před tím, než začnete.

* [Azure SQL Database se zapnutou Machine Learning Services (s R)](machine-learning-services-overview.md) .

* Balíček RevoScaleR – možnosti pro místní instalaci balíčku najdete v tématu [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) .

* Prostředí IDE jazyka R – tento kurz používá [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Nástroj SQL Query – v tomto kurzu se předpokládá, že používáte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Import ukázkové databáze

Ukázková datová sada použitá v tomto kurzu se uložila do záložního souboru databáze **. BacPac** , abyste ji mohli stáhnout a použít.

1. Stáhněte si soubor [databáze tutorialdb. BacPac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Postupujte podle pokynů v části [Import souboru BacPac do databáze v Azure SQL Database nebo spravované instanci Azure SQL](../../azure-sql/database/database-import.md)pomocí těchto podrobností:

   * Import ze souboru **databáze tutorialdb. BacPac** , který jste stáhli
   * Ve verzi Public Preview vyberte konfiguraci **Gen5/Vcore** pro novou databázi.
   * Pojmenujte novou databázi "databáze tutorialdb".

## <a name="load-the-data-into-a-data-frame"></a>Načtení dat do datového rámce

Chcete-li použít data v jazyce R, načtěte data z databáze do datového rámce ( `rentaldata` ).

Vytvořte nový soubor RScript v RStudio a spusťte následující skript. Nahraďte **Server**, **UID**a **PWD** vlastními informacemi o připojení.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Měly by se zobrazit výsledky podobné následujícímu.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Příprava dat

V této ukázkové databázi se většina přípravy už provedla, ale tady provedete ještě další přípravu.
Pomocí následujícího skriptu jazyka R Identifikujte tři sloupce jako *kategorie* změnou datových typů na *faktor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Měly by se zobrazit výsledky podobné následujícímu.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Data jsou teď připravená pro školení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v tomto kurzu, odstraňte databázi databáze tutorialdb ze serveru.

V Azure Portal postupujte podle následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** nebo **databáze SQL**.
1. Do pole **filtrovat podle názvu...** zadejte **databáze tutorialdb**a vyberte své předplatné.
1. Vyberte databázi databáze tutorialdb.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V první části této série kurzů jste dokončili tyto kroky:

* Import ukázkové databáze do databáze v Azure SQL Database pomocí jazyka R
* Načtení dat z databáze do datového rámce R
* Příprava dat v jazyce R určením některých sloupců jako kategorií

Pokud chcete vytvořit model strojového učení, který používá data z databáze databáze tutorialdb, postupujte podle části 2 této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)](predictive-model-build-compare-tutorial.md)
