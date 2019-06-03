---
title: 'Kurz: Nasadit model clusteringu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve třetí části této třídílné série nasadíte model clusteringu v R s Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419752"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Nasadit model clusteringu v R s Azure SQL Database Machine Learning Services (preview)

Ve třetí části této třídílné série nasadíte model clusteringu v R s Azure SQL Database Machine Learning Services (preview).

Vytvoříte uloženou proceduru s vložený skript R, který provádí clustering. Vzhledem k tomu, že váš model spouští ve službě Azure SQL database, můžete snadno školení pro data uložená v databázi.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Vytvořit uloženou proceduru, která generuje modelu
> * Provedení clustering ve službě SQL Database
> * Použití clusteringu informace

V [první část](sql-database-tutorial-clustering-model-prepare-data.md), jste zjistili, jak k přípravě dat ze služby Azure SQL database k provedení clustering v jazyce R.

V [druhá část](sql-database-tutorial-clustering-model-build.md), jste zjistili, jak vytvořit model K-Means provádět clustering.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Třetí části této série kurzů se předpokládá dokončení [ **první část** ](sql-database-tutorial-clustering-model-prepare-data.md) a [ **druhá část**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Vytvořit uloženou proceduru, která generuje modelu

Spusťte následující skript T-SQL Vytvořte uloženou proceduru. Postup znovu vytvoří kroky vyvinuté v části 1 a 2 v této sérii kurzů:

* klasifikovat zákazníci podle svých vyčerpá a vrátit historie
* Generovat čtyři clustery zákazníci, kteří používají K-Means algoritmus

Postup uloží výsledný clusteru mapování zákazníka v tabulce databáze **customer_return_clusters**.

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

## <a name="perform-clustering-in-sql-database"></a>Provedení clustering ve službě SQL Database

Teď, když jste vytvořili uložené procedury, spusťte následující skript k provedení clustering.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Ověřte, že funguje a že máme seznam zákazníků a jejich mapování clusteru.

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

## <a name="use-the-clustering-information"></a>Použití clusteringu informace

Protože jsou uložené clusteringu procedury v databázi, můžete to provést, efektivně clusteringu pro zákaznická data uložená ve stejné databázi. Proceduru lze provést vždy, když vaše zákaznická data se aktualizuje a aktualizované informace o clusteringu.

Předpokládejme, že chcete posílat propagační e-maily pro zákazníky v clusteru 3, skupiny, která má aktivní chování návratový (zobrazí se, jak bylo popsáno čtyři clustery v [druhá část](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). Následující kód vybere e-mailové adresy zákazníků v clusteru 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Můžete změnit **r.cluster** hodnota má být vrácen e-mailové adresy pro zákazníky v jiných clusterech.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s tímto kurzem, je odstranit databázi tpcxbb_1gb z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte podle těchto kroků:

1. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky** nebo **databází SQL**.
1. V **filtrovat podle názvu...**  zadejte **tpcxbb_1gb**a vyberte své předplatné.
1. Vyberte vaše **tpcxbb_1gb** databáze.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Ve třetí části této série kurzů dokončení těchto kroků:

* Vytvořit uloženou proceduru, která generuje modelu
* Provedení clustering ve službě SQL Database
* Použití clusteringu informace

Další informace o používání R v Azure SQL Database Machine Learning Services (preview) najdete v tématu:

* [Kurz: Příprava dat natrénujeme prediktivní model v R s Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Zápis pokročilé funkce jazyka R ve službě Azure SQL Database pomocí služby Machine Learning (preview)](sql-database-machine-learning-services-functions.md)
* [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)
* [Přidat balíček R do Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-add-r-packages.md)
