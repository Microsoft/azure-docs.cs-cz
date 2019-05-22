---
title: 'Kurz: Příprava dat natrénujeme prediktivní model v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V první části této série kurzů třemi částmi připravíte data ze služby Azure SQL database k natrénujeme prediktivní model v R s Azure SQL Database Machine Learning Services (preview).
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
ms.date: 05/02/2019
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978733"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Příprava dat natrénujeme prediktivní model v R s Azure SQL Database Machine Learning Services (preview)

V první části této série kurzů třemi částmi připravíte data ze služby Azure SQL database k natrénujeme prediktivní model v R s Azure SQL Database Machine Learning Services (preview).

Pro tuto řadu kurzů imagine vlastní obchodní pronájmů identifikátor klíče subjektu a vy chcete předpovědět počet pronajme, které budete mít na datum v budoucnosti. Tyto informace vám pomůže připravit akcií, zaměstnanci a zařízení.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Importovat ukázkovou databázi do Azure SQL database
> * Načtení dat z Azure SQL database do datového rámce pomocí jazyka R
> * Příprava dat díky identifikaci některé sloupce jako zařazené do kategorií

V [druhá část](sql-database-tutorial-predictive-model-build-compare.md), dozvíte se víc o vytvoření a trénování modelů více a poté vyberte požadovanou možnost co nejvíce zpřesnili.

V [třetí částí](sql-database-tutorial-predictive-model-deploy.md), dozvíte se víc o ukládání modelu v databázi a pak vytvořte uloženou proceduru, který umí vytvářet předpovědi na základě nových dat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

* Server Azure SQL Database s Machine Learning Services povolit – ve verzi public preview, Microsoft nebude připojení můžete povolit machine learning a pro stávající i nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

* Balíček RevoScaleR – viz [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) pro možnosti pro instalaci tohoto balíčku místně.

* Tento kurz používá prostředí IDE jazyka R - [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Nástroj pro dotaz SQL – tento kurz předpokládá, že používáte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importovat ukázkovou databázi

V tomto kurzu používá k datové sadě ukázka byly uloženy do souboru **.bacpac** záložní soubor databáze si můžete stáhnout a použít.

1. Stáhněte si soubor [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Postupujte podle pokynů v [Import souboru BACPAC k vytvoření služby Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-import), pomocí následující údaje:

   * Importovat z **TutorialDB.bacpac** jste stáhli
   * Ve verzi public preview, zvolte **Gen5 – VCOREúrovně/** konfigurace pro novou databázi
   * Název nové databáze "Databáze TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Načtení dat do datového rámce

Použít data v jazyce R, bude načtení dat z Azure SQL database do datového rámce (`rentaldata`).

Vytvořte nový soubor RScript v RStudio a spusťte následující skript. Nahraďte **Server**, **UID**, a **PWD** vlastní informace o připojení.

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

Měli byste vidět výsledky podobné následujícím.

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

V této ukázkové databáze již byla provedena většina přípravy, ale uděláte tady jeden další přípravy.
Pomocí následujícího skriptu jazyka R k identifikaci tři sloupce jako *kategorie* změnou datových typů *faktor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Měli byste vidět výsledky podobné následujícím.

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

Data je nyní připravený pro vzdělávání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat s tímto kurzem, odstranění databáze TutorialDB databáze z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte podle těchto kroků:

1. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky** nebo **databází SQL**.
1. V **filtrovat podle názvu...**  zadejte **databáze TutorialDB**a vyberte své předplatné.
1. Vyberte vaši databázi databáze TutorialDB.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V první části této série kurzů dokončení těchto kroků:

* Importovat záložní soubor databáze do Azure SQL database
* Načtení dat z Azure SQL database do datového rámce pomocí jazyka R
* Příprava dat díky identifikaci některé sloupce jako kategorie

Chcete-li vytvořit model, který používá data z databáze, databáze TutorialDB strojového učení, postupujte podle druhé části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prediktivního modelu v R s Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-build-compare.md)
