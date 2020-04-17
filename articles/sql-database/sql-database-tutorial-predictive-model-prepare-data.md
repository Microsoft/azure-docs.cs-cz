---
title: 'Kurz: Příprava dat pro trénování prediktivního modelu v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V první části této třídílné série kurzů připravíte data z databáze Azure SQL, abyste trénovali prediktivní model v R se službami Machine Learning Services azure SQL database (preview).
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
ms.openlocfilehash: bf69d2963c74723cb3fea542e28288e4f136d5c3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458756"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Příprava dat pro trénování prediktivního modelu v R se službami Azure SQL Database Machine Learning Services (preview)

V první části této třídílné série kurzů budete importovat a připravovat data z databáze Azure SQL pomocí R. Později v této řadě použijete tato data k trénování a nasazování prediktivního modelu strojového učení v R se službami Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Pro tuto sérii kurzů si představte, že vlastníte půjčovnu lyží a chcete předpovědět počet pronájmů, které budete mít v budoucnu. Tyto informace vám pomohou připravit vaše akcie, personál a zařízení.

V částech první a dvě této řady budete vyvíjet některé skripty R v RStudiu pro přípravu dat a trénování modelu strojového učení. Potom v části třetí, budete spouštět tyto skripty R uvnitř databáze SQL pomocí uložené procedury.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Import ukázkové databáze do databáze Azure SQL pomocí jazyka R
> * Načtení dat z databáze Azure SQL do datového rámce R
> * Příprava dat v R identifikací některých sloupců jako kategorických

Ve [druhé části](sql-database-tutorial-predictive-model-build-compare.md)se dozvíte, jak vytvořit a trénovat více modelů strojového učení v R a pak zvolte nejpřesnější modely.

V [části tři](sql-database-tutorial-predictive-model-deploy.md)se dozvíte, jak uložit model v databázi a potom vytvořit uložené procedury ze skriptů R, které jste vyvinuli v částech jedna a dvě. Uložené procedury budou spuštěny v databázi SQL, aby se předpovědi založené na nových datech.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/) než začnete.

* [Azure SQL Database se službami strojového učení (s povolenou R).](sql-database-machine-learning-services-overview.md)

* RevoScaleR balíček - Viz [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) pro možnosti instalace tohoto balíčku místně.

* R IDE - Tento kurz používá [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Sql dotaz nástroj – tento kurz předpokládá, že používáte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Import ukázkové databáze

Ukázková datová sada použitá v tomto kurzu byla uložena do záložního souboru databáze **.bacpac,** který můžete stáhnout a použít.

1. Stáhněte si soubor [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Podle pokynů v [části Import souboru BACPAC vytvořte databázi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-import)pomocí těchto podrobností:

   * Import ze staženého souboru **TutorialDB.bacpac**
   * Během veřejné verze Preview zvolte konfiguraci **Gen5/vCore** pro novou databázi.
   * Pojmenujte novou databázi "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Načtení dat do datového rámce

Chcete-li použít data v R, načtete data z databáze`rentaldata`Azure SQL do datového rámce ( ).

Vytvořte nový soubor RScript v RStudiu a spusťte následující skript. Nahraďte **server**, **UID**a **PWD** vlastními informacemi o připojení.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
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

Měli byste vidět výsledky podobné následujícímu.

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

V této ukázkové databázi již byla provedena většina přípravy, ale tady projdete ještě jednou přípravou.
Pomocí následujícího skriptu R můžete identifikovat tři sloupce jako *kategorie* změnou datových typů na *faktor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Měli byste vidět výsledky podobné následujícímu.

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

Data jsou nyní připravena k tréninku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v tomto kurzu, odstraňte databázi TutorialDB z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte takto:

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** nebo **databáze SQL**.
1. Do pole **Filtr podle názvu...** zadejte **TutorialDB**a vyberte předplatné.
1. Vyberte databázi TutorialDB.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V první části této série kurzů jste dokončili tyto kroky:

* Import ukázkové databáze do databáze Azure SQL pomocí jazyka R
* Načtení dat z databáze Azure SQL do datového rámce R
* Příprava dat v R identifikací některých sloupců jako kategorických

Chcete-li vytvořit model strojového učení, který používá data z databáze TutorialDB, postupujte podle druhé části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prediktivního modelu v R se službami Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-build-compare.md)
