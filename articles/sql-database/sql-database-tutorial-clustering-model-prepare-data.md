---
title: 'Kurz: Příprava dat k provedení řízení clusterů v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V první části této série kurzů třemi částmi připravíte data ze služby Azure SQL database k provedení řízení clusterů v R s Azure SQL Database Machine Learning Services (preview).
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
ms.date: 05/17/2019
ms.openlocfilehash: 83ef25f04012933c2665e63e4617d480eb336f7b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419458"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Příprava dat k provedení řízení clusterů v R s Azure SQL Database Machine Learning Services (preview)

V první části této série kurzů třemi částmi připravíte data ze služby Azure SQL database k provedení řízení clusterů v R s Azure SQL Database Machine Learning Services (preview).

*Clustering* lze vysvětlit jako uspořádání dat do skupin, ve kterém jsou členy skupiny podobné nějakým způsobem.
Budete používat **K-Means** algoritmus clustering zákazníci v datové sadě produkt koupí a vrátí. Pomocí clusteringu zákazníků, můžete se zaměřit svých marketingových aktivitách efektivněji pomocí cílení na konkrétní skupiny.
K-Means clustering je *supervize* algoritmus, který hledá vzory v datech podle podobnosti.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Importovat ukázkovou databázi do Azure SQL database
> * Samostatné zákazníci spolu různé dimenze
> * Načtení dat z Azure SQL database do datového rámce pomocí jazyka R

V [druhá část](sql-database-tutorial-clustering-model-build.md), dozvíte se víc o vytvoření a trénování modelu K-Means clusteringu.

V [třetí částí](sql-database-tutorial-clustering-model-deploy.md), se dozvíte, jak vytvořit uloženou proceduru v databázi Azure SQL, který může provádět clustering založené na nová data.

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

V tomto kurzu používá k datové sadě ukázka byly uloženy do souboru **.bacpac** záložní soubor databáze si můžete stáhnout a použít. Tato datová sada je odvozen z [tpcx bb](http://www.tpc.org/tpcx-bb/default.asp) poskytované datovou sadu [transakce zpracování výkonu Rady (TPC)](http://www.tpc.org/default.asp).

1. Stáhněte si soubor [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Postupujte podle pokynů v [Import souboru BACPAC k vytvoření služby Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-import), pomocí následující údaje:

   * Importovat z **tpcxbb_1gb.bacpac** jste stáhli
   * Ve verzi public preview, zvolte **Gen5 – VCOREúrovně/** konfigurace pro novou databázi
   * Název nové databáze "tpcxbb_1gb"

## <a name="separate-customers"></a>Samostatné zákazníků

Vytvořte nový soubor RScript v RStudio a spusťte následující skript.
V dotazu SQL rozdělujete zákazníků v dimenzích:

* **orderRatio** = vratky poměr (celkový počet objednávek částečně nebo zcela vrátil a celkový počet objednávek)
* **itemsRatio** = vrácené položky poměr (celkový počet položek vrácených oproti počet zakoupených položek)
* **monetaryRatio** = vrácenou částku poměr (celkovou peněžní hodnotu vrácených oproti velikosti zakoupených položek)
* **frekvence** = návratový frekvence

V **vložte** fungovat, nahraďte **Server**, **UID**, a **PWD** vlastní informace o připojení.

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

Teď pomocí následujícího skriptu pro vrácení výsledků z dotazu do pomocí snímků dat R **rxSqlServerData** funkce.
Jako součást procesu budete definice typu pro vybrané sloupce (s použitím colClasses) abyste měli jistotu, že typy jsou správně převedeny do jazyka R.

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

Měli byste vidět výsledky podobné následujícím.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

***Pokud nebudete pokračovat s tímto kurzem***, odstraňte databázi tpcxbb_1gb z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte podle těchto kroků:

1. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky** nebo **databází SQL**.
1. V **filtrovat podle názvu...**  zadejte **tpcxbb_1gb**a vyberte své předplatné.
1. Vyberte vaše **tpcxbb_1gb** databáze.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V první části této série kurzů dokončení těchto kroků:

* Importovat ukázkovou databázi do Azure SQL database
* Samostatné zákazníci spolu různé dimenze
* Načtení dat z Azure SQL database do datového rámce pomocí jazyka R

Při vytváření služby machine learning model, který používá tato data o zákaznících, postupujte podle druhé části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prediktivního modelu v R s Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-build.md)
