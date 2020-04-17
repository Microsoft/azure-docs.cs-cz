---
title: 'Kurz: Příprava dat k provádění clusteringu v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V první části této třídílné série kurzů připravíte data z databáze Azure SQL k provádění clusteringu v R se službami Azure SQL Database Machine Learning Services (preview).
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452872"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Příprava dat k provádění clusteringu v R se službami Azure SQL Database Machine Learning Services (preview)

V první části této třídílné série kurzů importujete a připravíte data z databáze Azure SQL pomocí R. Později v této řadě použijete tato data k trénování a nasazování modelu clusteringu v R se službami Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*Clustering* lze vysvětlit jako uspořádání dat do skupin, kde členové skupiny jsou podobné nějakým způsobem.
Algoritmus **K-Means** použijete k vytvoření clusteringu zákazníků v datové sadě nákupů a vrácení produktů. Seskupením zákazníků můžete své marketingové úsilí efektivněji zaměřit cílením na konkrétní skupiny.
K-Prostředky clustering je *bez dohledu učení* algoritmus, který hledá vzory v datech na základě podobností.

V částech první a dvě této řady budete vyvíjet některé skripty R v RStudiu pro přípravu dat a trénování modelu strojového učení. Potom v části třetí, budete spouštět tyto skripty R uvnitř databáze SQL pomocí uložené procedury.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Import ukázkové databáze do databáze Azure SQL
> * Oddělte zákazníky v různých rozměrech pomocí R
> * Načtení dat z databáze Azure SQL do datového rámce R

V [druhé části](sql-database-tutorial-clustering-model-build.md)se dozvíte, jak vytvořit a trénovat model clusteringu K-Means v R.

V [části třetí](sql-database-tutorial-clustering-model-deploy.md)se dozvíte, jak vytvořit uloženou proceduru v databázi Azure SQL, která může provádět clustering v R na základě nových dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/) než začnete.

* [Azure SQL Database se službami strojového učení (s povolenou R).](sql-database-machine-learning-services-overview.md)

* RevoScaleR balíček - Viz [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) pro možnosti instalace tohoto balíčku místně.

* R IDE - Tento kurz používá [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Sql dotaz nástroj – tento kurz předpokládá, že používáte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) nebo SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Import ukázkové databáze

Ukázková datová sada použitá v tomto kurzu byla uložena do záložního souboru databáze **.bacpac,** který můžete stáhnout a použít. Tato datová sada je odvozena z datového souboru [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) poskytnutého [Radou pro výkonnost zpracování transakcí (TPC).](http://www.tpc.org/default.asp)

1. Stáhněte soubor [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Podle pokynů v [části Import souboru BACPAC vytvořte databázi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-import)pomocí těchto podrobností:

   * Import ze staženého souboru **tpcxbb_1gb.bacpac**
   * Během veřejné verze Preview zvolte konfiguraci **Gen5/vCore** pro novou databázi.
   * Pojmenujte novou databázi "tpcxbb_1gb"

## <a name="separate-customers"></a>Samostatní zákazníci

Vytvořte nový soubor RScript v RStudiu a spusťte následující skript.
V dotazu SQL oddělujete zákazníky podle následujících dimenzí:

* **orderRatio** = poměr vratky (celkový počet částečně nebo plně vrácených objednávek oproti celkovému počtu objednávek)
* **itemsRatio** = poměr vrácených položek (celkový počet vrácených položek oproti počtu zakoupených položek)
* **monetaryRatio** = poměr vrácené částky (celková peněžní částka vrácených položek oproti nakoupené částce)
* **frekvence** = frekvence návratu

Ve funkci **vložení** nahraďte **server**, **UID**a **pwd** vlastními informacemi o připojení.

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

Nyní pomocí následujícího skriptu vraťte výsledky z dotazu do datového rámce R pomocí funkce **rxSqlServerData.**
Jako součást procesu definujete typ pro vybrané sloupce (pomocí colClasses) a ujistěte se, že typy jsou správně přeneseny do R.

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

Měli byste vidět výsledky podobné následujícímu.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

***Pokud nebudete pokračovat v tomto kurzu***, odstraňte tpcxbb_1gb databázi z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte takto:

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** nebo **databáze SQL**.
1. Do pole **Filtr podle názvu...** zadejte **tpcxbb_1gb**a vyberte předplatné.
1. Vyberte **databázi tpcxbb_1gb.**
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V první části této série kurzů jste dokončili tyto kroky:

* Import ukázkové databáze do databáze Azure SQL
* Oddělte zákazníky v různých rozměrech pomocí R
* Načtení dat z databáze Azure SQL do datového rámce R

Chcete-li vytvořit model strojového učení, který používá tato zákaznická data, postupujte podle druhé části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prediktivního modelu v R se službami Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-build.md)
