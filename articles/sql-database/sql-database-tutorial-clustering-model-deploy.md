---
title: 'Kurz: Nasazení modelu clusteringu v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve třetí části této třídílné série kurzů nasadíte model clusteringu v R se službami Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: ef478246108d40a0c97d7dab03ecf1e5b474410b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452889"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Nasazení modelu clusteringu v R se službami Machine Learning Services Azure SQL Database Machine Learning Services (preview)

Ve třetí části této třídílné série kurzů nasadíte model clusteringu vyvinutý v R do databáze SQL pomocí služby Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Vytvoříte uloženou proceduru s vloženým skriptem R, který provádí clustering. Vzhledem k tomu, že váš model se spustí v databázi Azure SQL, lze snadno trénovat proti datům uloženým v databázi.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření uložené procedury, která generuje model
> * Provádění clusterů v databázi SQL
> * Použití informací o clusteringu

V [první části](sql-database-tutorial-clustering-model-prepare-data.md)jste se naučili, jak připravit data z databáze Azure SQL k provádění clustering.

V [druhé části](sql-database-tutorial-clustering-model-build.md)jste se naučili, jak vytvořit a trénovat model clusteringu K-Means v R.

## <a name="prerequisites"></a>Požadavky

* Třetí část této série kurzů předpokládá, že jste dokončili [**první**](sql-database-tutorial-clustering-model-prepare-data.md) a [**druhá část**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Vytvoření uložené procedury, která generuje model

Spusťte následující skript T-SQL a vytvořte uloženou proceduru. Postup obnoví kroky, které jste vyvinuli v částech jedna a dvě z této série kurzů:

* klasifikovat zákazníky na základě jejich historie nákupů a vrácení
* generovat čtyři clustery zákazníků pomocí algoritmu K-Means

Procedura ukládá výsledná mapování clusteru zákazníků v tabulce databáze **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Provádění clusterů v databázi SQL

Teď, když jste vytvořili uloženou proceduru, spusťte následující skript k provedení clusteringu.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Ověřte, zda funguje a zda máme ve skutečnosti seznam zákazníků a jejich mapování clusteru.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Použití informací o clusteringu

Vzhledem k tomu, že jste uložili clustering postup v databázi, může provádět clustering efektivně proti zákaznická data uložená ve stejné databázi. Postup můžete provést při každé aktualizaci zákaznických dat a použít aktualizované informace o clusteringu.

Předpokládejme, že chcete odeslat propagační e-mail zákazníkům v clusteru 3, skupině, která má aktivnější návratové chování (můžete vidět, jak byly popsány čtyři clustery v [druhé části).](sql-database-tutorial-clustering-model-build.md#analyze-the-results) Následující kód vybere e-mailové adresy zákazníků v clusteru 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Hodnotu **r.clusteru** můžete změnit tak, aby zákazníkům v jiných clusterech vracela e-mailové adresy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až dokončíte tento kurz, můžete odstranit tpcxbb_1gb databázi z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte takto:

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** nebo **databáze SQL**.
1. Do pole **Filtr podle názvu...** zadejte **tpcxbb_1gb**a vyberte předplatné.
1. Vyberte **databázi tpcxbb_1gb.**
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve třetí části této série kurzů jste dokončili tyto kroky:

* Vytvoření uložené procedury, která generuje model
* Provádění clusterů v databázi SQL
* Použití informací o clusteringu

Další informace o používání jazyka R ve službě Azure SQL Database Machine Learning Services (preview) najdete v tématu:

* [Kurz: Příprava dat pro trénování prediktivního modelu v R se službami Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
* [Práce s daty R a SQL ve službě Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)
* [Přidání balíčku R do služby Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-add-r-packages.md)
