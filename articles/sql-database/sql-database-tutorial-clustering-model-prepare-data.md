---
title: 'Kurz: Příprava dat pro provádění clusteringu v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V první části této série výukových kurzů budete připravovat data z Azure SQL Database, abyste mohli provádět clusteringu v R s Azure SQL Database Machine Learning Services (Preview).
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: abe7d5ed1d4ba1308abde04aee32a3ea222456b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452872"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Příprava dat pro provádění clusteringu v R s Azure SQL Database Machine Learning Services (Preview)

V první části této tři série kurzů budete importovat a připravit data z Azure SQL Database pomocí jazyka R. Později v této sérii použijete tato data ke školení a nasazení modelu clusteringu v jazyce R s Azure SQL Database Machine Learning Services (Preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*Clustering* se dá vysvětlit jako uspořádání dat do skupin, kde jsou členové skupiny podobným způsobem.
K provedení clusteringu zákazníků v rámci datové sady nákupů produktů a vrácení se používá algoritmus **K** . Díky clusteringu zákazníkům můžete zaměřit své marketingové úsilí efektivněji tím, že zacílíte na konkrétní skupiny.
K-znamená, že clustering je *bezdohledový* algoritmus pro učení, který vyhledává vzory v datech na základě podobností.

V rámci jedné a dvou částí této série budete vyvíjet několik skriptů R v RStudio, abyste mohli připravit vaše data a naučit model strojového učení. Pak v části 3 spustíte tyto skripty R v databázi SQL pomocí uložených procedur.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Import ukázkové databáze do Azure SQL Database
> * Samostatné zákazníky v různých dimenzích pomocí jazyka R
> * Načtení dat z databáze SQL Azure do datového rámce R

V [druhé části](sql-database-tutorial-clustering-model-build.md)se dozvíte, jak vytvořit a naučit model pro clustering v jazyce R.

V [třetí části](sql-database-tutorial-clustering-model-deploy.md)se dozvíte, jak vytvořit uloženou proceduru ve službě Azure SQL Database, která může provádět clusteringu v R na základě nových dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/) před tím, než začnete.

* [Azure SQL Database se zapnutou Machine Learning Services (s R)](sql-database-machine-learning-services-overview.md) .

* Balíček RevoScaleR – možnosti pro místní instalaci balíčku najdete v tématu [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) .

* Prostředí IDE jazyka R – tento kurz používá [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Nástroj SQL Query – v tomto kurzu se předpokládá, že používáte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Import ukázkové databáze

Ukázková datová sada použitá v tomto kurzu se uložila do záložního souboru databáze **. BacPac** , abyste ji mohli stáhnout a použít. Tato datová sada je odvozena z datové sady [tpcx-BB](http://www.tpc.org/tpcx-bb/default.asp) poskytované [Radou výkonu zpracování transakcí (TPC)](http://www.tpc.org/default.asp).

1. Stáhněte soubor [tpcxbb_1gb. BacPac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Postupujte podle pokynů v části [Import souboru BacPac a vytvořte databázi SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import)pomocí těchto podrobností:

   * Import ze staženého souboru **tpcxbb_1gb. BacPac**
   * Ve verzi Public Preview vyberte konfiguraci **Gen5/Vcore** pro novou databázi.
   * Pojmenujte novou databázi tpcxbb_1gb.

## <a name="separate-customers"></a>Samostatné zákazníky

Vytvořte nový soubor RScript v RStudio a spusťte následující skript.
V dotazu SQL oddělíte zákazníky v následujících rozměrech:

* **orderRatio** = poměr návratové objednávky (celkový počet poslaných částečně nebo úplně vrácených objednávek oproti celkovému počtu objednávek)
* **itemsRatio** = poměr návratové položky (celkový počet vrácených položek oproti počtu zakoupených položek)
* **monetaryRatio** = poměr návratové hodnoty (celková peněžní částka vrácených položek oproti zakoupené množství)
* **frekvence** = návratová frekvence

Ve funkci **vložení** nahraďte **Server**, **UID**a **PWD** vlastními informacemi o připojení.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>Načtení dat do datového rámce

Nyní pomocí následujícího skriptu vraťte výsledky z dotazu do datového rámce R pomocí funkce **rxSqlServerData** .
V rámci procesu definujete typ pro vybrané sloupce (pomocí colClasses), abyste se ujistili, že jsou typy správně přeneseny do jazyka R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Měly by se zobrazit výsledky podobné následujícímu.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete ***pokračovat v tomto kurzu***, odstraňte databázi tpcxbb_1gb ze serveru Azure SQL Database.

V Azure Portal postupujte podle následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** nebo **databáze SQL**.
1. Do pole **filtrovat podle názvu...** zadejte **tpcxbb_1gb**a vyberte své předplatné.
1. Vyberte svou databázi **tpcxbb_1gb** .
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V první části této série kurzů jste dokončili tyto kroky:

* Import ukázkové databáze do Azure SQL Database
* Samostatné zákazníky v různých dimenzích pomocí jazyka R
* Načtení dat z databáze SQL Azure do datového rámce R

Pokud chcete vytvořit model strojového učení, který používá tato zákaznická data, postupujte podle části 2 této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)](sql-database-tutorial-clustering-model-build.md)
