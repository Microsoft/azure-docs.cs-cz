---
title: 'Kurz: Nasazení prediktivního modelu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve třetí části tohoto kurzu se třemi částmi nasadíte prediktivní model v jazyce R s Azure SQL Database Machine Learning Services (Preview).
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
ms.openlocfilehash: 84c0f6564fc9ab8abc43205b58d9445cda82a2da
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253813"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Nasazení prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V třetí části tohoto výukového kurzu nasadíte prediktivní model vyvinutý v jazyce R do databáze v Azure SQL Database pomocí Azure SQL Database Machine Learning Services (Preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Uloženou proceduru vytvoříte pomocí vloženého skriptu jazyka R, který vytváří předpovědi pomocí modelu. Vzhledem k tomu, že se váš model spouští v databázi, můžete ho snadno vyškolet pro data uložená v databázi.

V tomto článku se naučíte, jak pomocí skriptů R, které jste vytvořili v částech One a 2, jak:

> [!div class="checklist"]
>
> * Vytvořit uloženou proceduru, která generuje model strojového učení
> * Uložení modelu v databázové tabulce
> * Vytvořit uloženou proceduru, která provede předpovědi pomocí modelu
> * Spuštění modelu s novými daty

V [první části](predictive-model-prepare-data-tutorial.md)jste zjistili, jak naimportovat ukázkovou databázi a potom připravit data, která se mají používat k výuce prediktivního modelu v jazyce R.

V [druhé části](predictive-model-build-compare-tutorial.md)jste zjistili, jak vytvořit a naučit více modelů strojového učení v jazyce R, a pak zvolíte nejpřesnější.

## <a name="prerequisites"></a>Požadavky

* Třetí část této série kurzů předpokládá, že jste dokončili [**jednu**](predictive-model-prepare-data-tutorial.md) a [**druhou část**](predictive-model-build-compare-tutorial.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Vytvořit uloženou proceduru, která generuje model

Ve druhé části této série kurzů jste se rozhodli, že model rozhodovacího stromu (dtree) byl nejpřesnější. Nyní pomocí skriptů jazyka R, které jste vyvinuli, vytvořte uloženou proceduru ( `generate_rental_rx_model` ), která bude vlakem a vygenerovat model dtree pomocí rxDTree z balíčku RevoScaleR.

Spusťte následující příkazy v Azure Data Studio nebo SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Uložení modelu v databázové tabulce

Vytvořte tabulku v databázi databáze tutorialdb a potom tento model uložte do tabulky.

1. Vytvořte tabulku ( `rental_rx_models` ) pro uložení modelu.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Uložte model do tabulky jako binární objekt s názvem modelu "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;

    DECLARE @model VARBINARY(MAX);

    EXECUTE generate_rental_rx_model @model OUTPUT;

    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );

    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Vytvořit uloženou proceduru, která provede předpovědi

Vytvořte uloženou proceduru ( `predict_rentalcount_new` ), která vytvoří předpovědi s využitím trained model a sady nových dat.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Spuštění modelu s novými daty

Nyní můžete použít uloženou proceduru `predict_rentalcount_new` pro předpověď počtu pronájmu z nových dat.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Měl by se zobrazit výsledek podobný následujícímu.

```results
RentalCount_Predicted
332.571428571429
```

Úspěšně jste vytvořili, proučeni a nasadili model v databázi v Azure SQL Database. Pak jste tento model použili v uložené proceduře k předpovědi hodnot na základě nových dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání databáze databáze tutorialdb ji odstraňte ze serveru.

V Azure Portal postupujte podle následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** nebo **databáze SQL**.
1. Do pole **filtrovat podle názvu...** zadejte **databáze tutorialdb**a vyberte své předplatné.
1. Vyberte databázi databáze tutorialdb.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V třetí části této série kurzů jste dokončili tyto kroky:

* Vytvořit uloženou proceduru, která generuje model strojového učení
* Uložení modelu v databázové tabulce
* Vytvořit uloženou proceduru, která provede předpovědi pomocí modelu
* Spuštění modelu s novými daty

Další informace o používání jazyka R v Azure SQL Database Machine Learning Services (Preview) najdete v tématech:

* [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](machine-learning-services-functions.md)
* [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (Preview)](machine-learning-services-data-issues.md)
* [Přidání balíčku R do Azure SQL Database Machine Learning Services (Preview)](machine-learning-services-add-r-packages.md)
