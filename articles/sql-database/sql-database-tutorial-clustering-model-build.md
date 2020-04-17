---
title: 'Kurz: Vytvoření modelu clusteringu v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve druhé části této třídílné série kurzů vytvoříte model K-Means k provádění clusteringu v R se službami Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453093"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Vytvoření modelu clusteringu v R se službami Azure SQL Database Machine Learning Services (preview)

Ve druhé části této třídílné série kurzů vytvoříte model K-Means v R k provedení clusteringu. V další části této řady nasadíte tento model v databázi SQL se službou Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Definovat počet clusterů pro algoritmus K-Means
> * Provádět clustering
> * Analýza výsledků

V [první části](sql-database-tutorial-clustering-model-prepare-data.md)jste se naučili, jak připravit data z databáze Azure SQL k provádění clustering.

V [části třetí](sql-database-tutorial-clustering-model-deploy.md)se dozvíte, jak vytvořit uloženou proceduru v databázi Azure SQL, která může provádět clustering v R na základě nových dat.

## <a name="prerequisites"></a>Požadavky

* Druhá část tohoto kurzu předpokládá, že jste dokončili [**první část**](sql-database-tutorial-clustering-model-prepare-data.md) a její předpoklady.

## <a name="define-the-number-of-clusters"></a>Definovat počet clusterů

Chcete-li sclusterovat vaše zákaznická data, použijete algoritmus clusteringu **K-Means,** což je jeden z nejjednodušších a nejznámějších způsobů seskupování dat.
Můžete si přečíst více o K-Prostředky v [kompletní průvodce K-znamená clustering algoritmus](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritmus přijímá dva vstupy: samotná data a předdefinované číslo "*k*" představující počet clusterů, které mají být generovány.
Výstupem jsou *clustery k* se vstupními daty rozdělenými mezi clustery.

Chcete-li zjistit počet clusterů pro algoritmus použít, použijte obrázek v rámci skupiny součtu čtverců, podle počtu clusterů extrahovaných. Příslušný počet clusterů, které mají být vytryskány, je v ohybu nebo "kolenu" parcely.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Loketní graf](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Na základě grafu to vypadá, že *k = 4* by bylo dobrou hodnotou vyzkoušet. Tato hodnota *k* seskupila zákazníky do čtyř clusterů.

## <a name="perform-clustering"></a>Provádět clustering

V následujícím skriptu R použijete funkci **rxKmeans**, což je funkce K-Means v balíčku RevoScaleR.

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

Nyní, když jste provedli clustering pomocí K-Means, dalším krokem je analyzovat výsledek a zjistit, zda můžete najít nějaké užitečné informace.

**Clust** objekt obsahuje výsledky z K-Prostředky clustering.

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

Čtyři prostředky seskupení jsou uvedeny pomocí proměnných definovaných v [části 1](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = poměr vratky (celkový počet částečně nebo plně vrácených objednávek oproti celkovému počtu objednávek)
* *itemsRatio* = poměr vrácených položek (celkový počet vrácených položek oproti počtu zakoupených položek)
* *monetaryRatio* = poměr vrácené částky (celková peněžní částka vrácených položek oproti nakoupené částce)
* *frekvence* = frekvence návratu

Dolování dat pomocí K-Prostředky často vyžaduje další analýzu výsledků a další kroky k lepšímu pochopení každého clusteru, ale může poskytnout některé dobré vede.
Zde je několik způsobů, jak můžete interpretovat tyto výsledky:

* Cluster 1 (největší cluster) se zdá být skupinou zákazníků, kteří nejsou aktivní (všechny hodnoty jsou nulové).
* Cluster 3 se zdá být skupina, která vyniká, pokud jde o chování návratu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

***Pokud nebudete pokračovat v tomto kurzu***, odstraňte tpcxbb_1gb databázi z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte takto:

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** nebo **databáze SQL**.
1. Do pole **Filtr podle názvu...** zadejte **tpcxbb_1gb**a vyberte předplatné.
1. Vyberte **databázi tpcxbb_1gb.**
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve druhé části této série kurzů jste dokončili tyto kroky:

* Definovat počet clusterů pro algoritmus K-Means
* Provádět clustering
* Analýza výsledků

Chcete-li nasadit model strojového učení, který jste vytvořili, postupujte podle třetí části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Nasazení modelu clusteringu v R se službami Machine Learning Services Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-deploy.md)