---
title: 'Kurz: Trénování a porovnat prediktivních modelů v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve druhé části této série kurzů třemi částmi vytvoříte dva prediktivních modelů v R s Azure SQL Database Machine Learning Services (preview) a vyberte co nejvíce zpřesnili modelu.
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
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957607"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Vytvoření prediktivního modelu v R s Azure SQL Database Machine Learning Services (preview)

Ve druhé části této série kurzů třemi částmi vytvoříte dva prediktivních modelů v R s Azure SQL Database Machine Learning Services (preview) a vyberte co nejvíce zpřesnili modelu.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Trénování dva modely strojového učení
> * Vytváření předpovědí z obou modelů
> * Porovnejte výsledky rozhodnout se, co nejvíce zpřesnili modelu

V [první část](sql-database-tutorial-predictive-model-prepare-data.md), jste zjistili, jak importovat ukázkovou databázi do Azure SQL database a poté si připravte data, která má být použit pro trénování modelu prediktivní v jazyce R.

V [třetí částí](sql-database-tutorial-predictive-model-deploy.md), dozvíte se víc o ukládání modelu v databázi a pak vytvořte uloženou proceduru, který umí vytvářet předpovědi na základě nových dat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Druhá část tohoto kurzu se předpokládá dokončení [ **první část** ](sql-database-tutorial-predictive-model-prepare-data.md) a nezbytný software.

## <a name="train-two-models"></a>Trénování dva modely

Najít nejlepší model pro uci identifikátor klíče subjektu, vytvořte dva různé modely (lineární regrese a rozhodovací strom) a zjistěte, která z nich je předpověď přesněji. Použijete-li datový rámec `rentaldata` , které jste vytvořili v první části této série.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Vytváření předpovědí z obou modelů

Pomocí funkce predict predict, které pronájem se počítá pomocí jednotlivých trénovaného modelu.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Porovnejte výsledky

Nyní chcete zobrazit, který modelů poskytuje nejlepší předpovědi. Rychlý a snadný způsob, jak to udělat je použití základní zobrazování funkce k zobrazení rozdílu mezi skutečnými hodnotami v trénovacích dat a předpovězeným hodnotám.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Porovnání dvou modelů](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Zdá se, je model stromu rozhodnutí přesnější ze dvou modelů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat s tímto kurzem, odstranění databáze TutorialDB databáze z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte podle těchto kroků:

1. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky** nebo **databází SQL**.
1. V **filtrovat podle názvu...**  zadejte **databáze TutorialDB**a vyberte své předplatné.
1. Vyberte vaši databázi databáze TutorialDB.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Ve druhé části této série kurzů dokončení těchto kroků:

* Trénování dva modely strojového učení
* Vytváření předpovědí z obou modelů
* Porovnejte výsledky rozhodnout se, co nejvíce zpřesnili modelu

Pokud chcete nasadit model machine learning, který jste vytvořili, použijte třetí části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Nasazení prediktivního modelu v R s Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-deploy.md)
