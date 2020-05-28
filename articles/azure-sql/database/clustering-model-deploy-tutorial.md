---
title: 'Kurz: nasazení modelu clusteringu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V třetí části této série kurzů se třemi částmi nasadíte model clusteringu v jazyce R s Azure SQL Database Machine Learning Services (Preview).
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
ms.openlocfilehash: d5227fc89d99257f3390820d4930c3d57b63f03d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053397"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: nasazení modelu clusteringu v jazyce R s Azure SQL Database Machine Learning Services (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V třetí části této série výukových kurzů nasadíte model clusteringu vyvinutý v jazyce R do databáze SQL pomocí Azure SQL Database Machine Learning Services (Preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Uloženou proceduru vytvoříte pomocí vloženého skriptu jazyka R, který provádí clusteringu. Vzhledem k tomu, že se váš model spouští ve službě Azure SQL Database, můžete ho snadno vyškolet s daty uloženými v databázi.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvořit uloženou proceduru, která generuje model
> * Provedení clusteringu v SQL Database
> * Použití informací o clusteringu

V [první části](clustering-model-prepare-data-tutorial.md)jste zjistili, jak připravit data z databáze SQL Azure pro provádění clusteringu.

V [druhé části](clustering-model-build-tutorial.md)jste zjistili, jak vytvořit a naučit model pro clustering v jazyce R.

## <a name="prerequisites"></a>Požadavky

* Třetí část této série kurzů předpokládá, že jste dokončili [**jednu**](clustering-model-prepare-data-tutorial.md) a [**druhou část**](clustering-model-build-tutorial.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Vytvořit uloženou proceduru, která generuje model

Spuštěním následujícího skriptu T-SQL vytvořte uloženou proceduru. Procedura znovu vytvoří kroky, které jste vytvořili v částech jedna a dvě z této série kurzů:

* klasifikace zákazníků na základě jejich nákupu a návratové historie
* generovat čtyři clustery zákazníků pomocí algoritmu K

Procedura ukládá výsledná mapování clusterů zákazníků do tabulky databáze **customer_return_clusters**.

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

## <a name="perform-clustering-in-sql-database"></a>Provedení clusteringu v SQL Database

Teď, když jste vytvořili uloženou proceduru, spusťte následující skript, který provede clusteringu.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Ověřte, že funguje a že ve skutečnosti máme seznam zákazníků a jejich mapování clusteru.

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

Vzhledem k tomu, že jste v databázi uložili postup clusteringu, můžete efektivně provádět clustering proti zákaznickým datům uloženým ve stejné databázi. Postup můžete provést vždy, když se aktualizují zákaznická data a použijí se aktualizované informace o clusteringu.

Předpokládejme, že chcete odeslat propagační e-mail zákazníkům v clusteru 3, skupině s více aktivními návratovými chováními (vidíte, jak byly čtyři clustery popsány v [části druhá část](clustering-model-build-tutorial.md#analyze-the-results)). Následující kód vybere e-mailové adresy zákazníků v clusteru 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Hodnotu **r. cluster** můžete změnit tak, aby vracela e-mailové adresy pro zákazníky v jiných clusterech.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto kurzem hotovi, můžete odstranit databázi tpcxbb_1gb ze serveru.

V Azure Portal postupujte podle následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** nebo **databáze SQL**.
1. Do pole **filtrovat podle názvu...** zadejte **tpcxbb_1gb**a vyberte své předplatné.
1. Vyberte svou databázi **tpcxbb_1gb** .
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V třetí části této série kurzů jste dokončili tyto kroky:

* Vytvořit uloženou proceduru, která generuje model
* Provedení clusteringu v SQL Database
* Použití informací o clusteringu

Další informace o používání jazyka R v Azure SQL Database Machine Learning Services (Preview) najdete v tématech:

* [Kurz: Příprava dat pro výuku prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)](predictive-model-prepare-data-tutorial.md)
* [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](machine-learning-services-functions.md)
* [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (Preview)](machine-learning-services-data-issues.md)
* [Přidání balíčku R do Azure SQL Database Machine Learning Services (Preview)](machine-learning-services-add-r-packages.md)
