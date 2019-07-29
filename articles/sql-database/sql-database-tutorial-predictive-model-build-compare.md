---
title: 'Kurz: Školení a porovnání prediktivních modelů v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V druhé části této série výukových kurzů vytvoříte dva prediktivní modely v R s Azure SQL Database Machine Learning Services (Preview) a pak vyberete nejpřesnější model.
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
ms.openlocfilehash: 2c85a378dc219e8af1b6458344ee4dba0fa73e68
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596796"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Vytvoření prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)

Ve druhé části této série výukových kurzů vytvoříte dva prediktivní modely v jazyce R a vyberete nejpřesnější model. V další části této série tento model nasadíte do databáze SQL pomocí Azure SQL Database Machine Learning Services (Preview).

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Výuka dvou modelů strojového učení
> * Vytvoření předpovědi z obou modelů
> * Porovnání výsledků a výběr nejpřesnější modelu

V [první části](sql-database-tutorial-predictive-model-prepare-data.md)jste zjistili, jak naimportovat ukázkovou databázi a potom připravit data, která se mají používat k výuce prediktivního modelu v jazyce R.

V [třetí části](sql-database-tutorial-predictive-model-deploy.md)se dozvíte, jak uložit model do databáze a pak vytvořit uložené procedury z skriptů R, které jste vytvořili v částech One a 2. Uložené procedury se spustí v databázi SQL, aby se předpovědi na základě nových dat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Druhá část tohoto kurzu předpokládá, že jste dokončili [**část**](sql-database-tutorial-predictive-model-prepare-data.md) a její požadavky.

## <a name="train-two-models"></a>Výuka dvou modelů

Chcete-li najít nejlepší model pro data o pronájmu, vytvořte dva různé modely (lineární regresi a rozhodovací strom) a zjistěte, který z nich je předpověď přesnější. Použijete datový rámec `rentaldata` , který jste vytvořili v části jedné z těchto řad.

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

## <a name="make-predictions-from-both-models"></a>Vytvoření předpovědi z obou modelů

Pomocí funkce prediktivního odhadu počtu zapůjčení můžete použít každý vyškolený model.

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

## <a name="compare-the-results"></a>Porovnat výsledky

Nyní chcete zjistit, které modely poskytují nejlepší předpovědi. Rychlý a snadný způsob, jak to provést, je použít základní funkci vykreslení k zobrazení rozdílu mezi skutečnými hodnotami vašich školicích dat a předpovězenými hodnotami.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Porovnání dvou modelů](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Vypadá to, že model rozhodovacího stromu je přesnější z těchto dvou modelů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v tomto kurzu, odstraňte databázi databáze tutorialdb ze serveru Azure SQL Database.

V Azure Portal postupujte podle následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** nebo **databáze SQL**.
1. Do pole **filtrovat podle názvu...** zadejte **databáze tutorialdb**a vyberte své předplatné.
1. Vyberte databázi databáze tutorialdb.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Ve druhé části této série kurzů jste dokončili tyto kroky:

* Výuka dvou modelů strojového učení
* Vytvoření předpovědi z obou modelů
* Porovnání výsledků a výběr nejpřesnější modelu

Pokud chcete nasadit model strojového učení, který jste vytvořili, postupujte podle třetí části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz: Nasazení prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)](sql-database-tutorial-predictive-model-deploy.md)
