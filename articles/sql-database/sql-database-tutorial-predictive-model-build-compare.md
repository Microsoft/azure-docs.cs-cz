---
title: 'Výuka: Trénovat a porovnávat prediktivní modely v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve druhé části této třídílné série kurzů vytvoříte dva prediktivní modely v R s Azure SQL Database Machine Learning Services (preview) a pak vyberete nejpřesnější model.
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0985b37280e3cd363ba1728a5ec33b0012611ab2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452923"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Vytvoření prediktivního modelu v R se službami Azure SQL Database Machine Learning Services (preview)

Ve druhé části této třídílné série kurzů vytvoříte dva prediktivní modely v R a vyberete nejpřesnější model. V další části této řady nasadíte tento model v databázi SQL se službou Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Školení dvou modelů strojového učení
> * Předvídací ceny z obou modelů
> * Porovnání výsledků pro výběr nejpřesnějšího modelu

V [první části](sql-database-tutorial-predictive-model-prepare-data.md)jste se naučili importovat ukázkovou databázi a potom připravit data, která mají být použita pro trénování prediktivního modelu v R.

V [části tři](sql-database-tutorial-predictive-model-deploy.md)se dozvíte, jak uložit model v databázi a potom vytvořit uložené procedury ze skriptů R, které jste vyvinuli v částech jedna a dvě. Uložené procedury budou spuštěny v databázi SQL, aby se předpovědi založené na nových datech.

## <a name="prerequisites"></a>Požadavky

* Druhá část tohoto kurzu předpokládá, že jste dokončili [**první část**](sql-database-tutorial-predictive-model-prepare-data.md) a její předpoklady.

## <a name="train-two-models"></a>Vlak dva modely

Chcete-li najít nejlepší model pro data zapůjčení lyží, vytvořte dva různé modely (lineární regresní a rozhodovací strom) a zjistěte, který z nich předpovídá přesněji. Budete používat datový rámec, `rentaldata` který jste vytvořili v první části této řady.

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

## <a name="make-predictions-from-both-models"></a>Předvídací ceny z obou modelů

Pomocí funkce predict můžete předpovědět počty pronájmů pomocí každého trénovaného modelu.

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

Nyní chcete zjistit, který z modelů dává nejlepší předpovědi. Rychlý a snadný způsob, jak to provést, je použít základní funkci vykreslování k zobrazení rozdílu mezi skutečnými hodnotami v trénovacích datech a předpovídanými hodnotami.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Porovnání dvou modelů](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Vypadá to, že rozhodovací strom model je přesnější ze dvou modelů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v tomto kurzu, odstraňte databázi TutorialDB z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte takto:

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** nebo **databáze SQL**.
1. Do pole **Filtr podle názvu...** zadejte **TutorialDB**a vyberte předplatné.
1. Vyberte databázi TutorialDB.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve druhé části této série kurzů jste dokončili tyto kroky:

* Školení dvou modelů strojového učení
* Předvídací ceny z obou modelů
* Porovnání výsledků pro výběr nejpřesnějšího modelu

Chcete-li nasadit model strojového učení, který jste vytvořili, postupujte podle třetí části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Nasazení prediktivního modelu v R se službami Machine Learning Services azure SQL database (preview)](sql-database-tutorial-predictive-model-deploy.md)
