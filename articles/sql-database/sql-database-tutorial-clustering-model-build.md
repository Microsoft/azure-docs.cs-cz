---
title: 'Kurz: Sestavit model clusteringu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V druhé části této série kurzů třemi částmi sestavíte model K-Means provádět řízení clusterů v R s Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419759"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Sestavit model clusteringu v R s Azure SQL Database Machine Learning Services (preview)

V druhé části této série kurzů třemi částmi sestavíte model K-Means provádět řízení clusterů v R s Azure SQL Database Machine Learning Services (preview).

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Definuje počet clusterů pro algoritmus K-Means
> * Provedení clustering
> * Analýza výsledků

V [první část](sql-database-tutorial-clustering-model-prepare-data.md), jste zjistili, jak k přípravě dat ze služby Azure SQL database k provedení clustering v jazyce R.

V [třetí částí](sql-database-tutorial-clustering-model-deploy.md), se dozvíte, jak vytvořit uloženou proceduru v databázi Azure SQL, který může provádět clustering založené na nová data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Druhá část tohoto kurzu se předpokládá dokončení [ **první část** ](sql-database-tutorial-clustering-model-prepare-data.md) a nezbytný software.

## <a name="define-the-number-of-clusters"></a>Definuje počet clusterů

Seskupit do clusteru vaše zákaznická data, použijete **K-Means** algoritmu clusteringu, jedním z nejjednodušší a dobře známý způsob seskupení dat.
Další informace o K-Means v [kompletní pokyny k algoritmu clusteringu K-means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritmus přijímá dva vstupy: Vlastní data a předem definované číslo "*k*" reprezentující počet clusterů, které mají generovat.
Výstup je *k* clustery se vstupní data rozdělená mezi clustery.

Pokud chcete zjistit počet clusterů pro použití algoritmu, použití se graf v rámci skupiny součet kvadratických hodnot, podle počtu clusterů extrahovat. Odpovídající počet clusterů používat je na ohyb nebo "Pravoúhlá" grafu.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Pravoúhlá grafu](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Na základě grafu, vypadá jako *tisíc = 4* by bylo dobré hodnotu a opakujte. Že *k* hodnotu seskupí zákazníků do čtyři clustery.

## <a name="perform-clustering"></a>Provedení clustering

V následujícím skriptu R, budete používat funkci **rxKmeans**, což je funkce K-Means v balíčku RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analýza výsledků

Teď, když jste provedli, clustering pomocí K-Means, dalším krokem je k analýze výsledek a zobrazit, jestli nenajdete žádné užitečné informace.

**Clust** objekt obsahuje výsledky z K-Means clustering.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Čtyři clusteru prostředky jsou vzhledem pomocí proměnné definované v [první část](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = vratky poměr (celkový počet objednávek částečně nebo zcela vrátil a celkový počet objednávek)
* *itemsRatio* = vrácené položky poměr (celkový počet položek vrácených oproti počet zakoupených položek)
* *monetaryRatio* = vrácenou částku poměr (celkovou peněžní hodnotu vrácených oproti velikosti zakoupených položek)
* *frekvence* = návratový frekvence

Dolování dat pomocí K-Means často vyžaduje další analýzy výsledků a další kroky, abyste lépe pochopili každý cluster, ale může poskytovat některé dobré potenciálních zákazníků.
Tady je několik způsobů, jak může interpretovat tyto výsledky:

* Cluster 1 (na největší cluster) se zdá být skupinu zákazníků, které nejsou aktivní (všechny hodnoty jsou nula).
* Cluster 3 se zdá být skupinu, která odlišuje z hlediska návratový chování.

## <a name="clean-up-resources"></a>Vyčištění prostředků

***Pokud nebudete pokračovat s tímto kurzem***, odstraňte databázi tpcxbb_1gb z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte podle těchto kroků:

1. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky** nebo **databází SQL**.
1. V **filtrovat podle názvu...**  zadejte **tpcxbb_1gb**a vyberte své předplatné.
1. Vyberte vaše **tpcxbb_1gb** databáze.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Ve druhé části této série kurzů dokončení těchto kroků:

* Definuje počet clusterů pro algoritmus K-Means
* Provedení clustering
* Analýza výsledků

Pokud chcete nasadit model machine learning, který jste vytvořili, použijte třetí části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Nasadit model clusteringu v R s Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-deploy.md)