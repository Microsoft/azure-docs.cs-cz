---
title: 'Kurz: sestavení modelu clusteringu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve třetí části této série výukových kurzů sestavíte model K, který provádí clusteringu v R s Azure SQL Database Machine Learning Services (Preview).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453093"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: sestavení modelu clusteringu v R s Azure SQL Database Machine Learning Services (Preview)

Ve třetí části této série výukových kurzů sestavíte model K, v jazyce R, který provádí clusteringu. V další části této série tento model nasadíte do databáze SQL pomocí Azure SQL Database Machine Learning Services (Preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Zadejte počet clusterů pro algoritmus K.
> * Provedení clusteringu
> * Analýza výsledků

V [první části](sql-database-tutorial-clustering-model-prepare-data.md)jste zjistili, jak připravit data z databáze SQL Azure pro provádění clusteringu.

V [třetí části](sql-database-tutorial-clustering-model-deploy.md)se dozvíte, jak vytvořit uloženou proceduru ve službě Azure SQL Database, která může provádět clusteringu v R na základě nových dat.

## <a name="prerequisites"></a>Požadavky

* Druhá část tohoto kurzu předpokládá, že jste dokončili [**část**](sql-database-tutorial-clustering-model-prepare-data.md) a její požadavky.

## <a name="define-the-number-of-clusters"></a>Definovat počet clusterů

Chcete-li seskupit zákaznická data, použijte **k** tomu algoritmus pro clusteringu, jeden z nejjednodušších a nejpokročilejších způsobů seskupování dat.
Můžete si přečíst více o tom, jak to znamená v [kompletní příručce k nasazení k-znamená algoritmus clusteringu](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algoritmus přijímá dva vstupy: samotná data a předdefinované číslo "*k*" představující počet clusterů, které se mají vygenerovat.
Výstupem je *t clustery se* vstupními daty rozdělenými mezi clustery.

Chcete-li určit počet clusterů, které mají použít algoritmus, použijte vykreslení součtu čtverců v rámci skupin podle počtu extrahovaných clusterů. Příslušný počet clusterů, které se mají použít, je v ohybu nebo pravoúhlém zobrazení.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Graf pravoúhlého grafu](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

V závislosti na grafu vypadá to, že se jedná o dobrou hodnotu pro vyzkoušení *= 4* . Tato hodnota *k* bude seskupovat zákazníky do čtyř clusterů.

## <a name="perform-clustering"></a>Provedení clusteringu

V následujícím skriptu jazyka R budete používat funkci **rxKmeans**, což je funkce K, která je v balíčku RevoScaleR.

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

Teď, když jste provedli clusteringu pomocí K-znamenají, je dalším krokem analýza výsledku a zjištění, jestli můžete najít informace, které se dají dělat.

Objekt **clust** obsahuje výsledky z části k-znamená clustering.

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

Čtyři clustery znamenají použití proměnných definovaných v [části 1](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = poměr návratové objednávky (celkový počet poslaných částečně nebo úplně vrácených objednávek oproti celkovému počtu objednávek)
* *itemsRatio* = poměr návratové položky (celkový počet vrácených položek oproti počtu zakoupených položek)
* *monetaryRatio* = poměr návratové hodnoty (celková peněžní částka vrácených položek oproti zakoupené množství)
* *frekvence* = návratová frekvence

Dolování dat pomocí pro je často potřeba k další analýze výsledků a dalším krokům pro lepší pochopení jednotlivých clusterů, ale může poskytovat nějaké dobré zájemce.
Tady je několik způsobů, jak můžete interpretovat tyto výsledky:

* Cluster 1 (největší cluster) se jeví jako skupina zákazníků, kteří nejsou aktivní (všechny hodnoty jsou nulové).
* Cluster 3 se jeví jako skupina, která se nachází v souvislosti s chováním při vracení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete ***pokračovat v tomto kurzu***, odstraňte databázi tpcxbb_1gb ze serveru Azure SQL Database.

V Azure Portal postupujte podle následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** nebo **databáze SQL**.
1. Do pole **filtrovat podle názvu...** zadejte **tpcxbb_1gb**a vyberte své předplatné.
1. Vyberte svou databázi **tpcxbb_1gb** .
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve druhé části této série kurzů jste dokončili tyto kroky:

* Zadejte počet clusterů pro algoritmus K.
* Provedení clusteringu
* Analýza výsledků

Pokud chcete nasadit model strojového učení, který jste vytvořili, postupujte podle třetí části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: nasazení modelu clusteringu v jazyce R s Azure SQL Database Machine Learning Services (Preview)](sql-database-tutorial-clustering-model-deploy.md)