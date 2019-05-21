---
title: 'Kurz: Nasazení prediktivního modelu v jazyce R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve třetí části této třídílném kurzu budete nasazovat prediktivní model v R s Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978685"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Nasazení prediktivního modelu v R s Azure SQL Database Machine Learning Services (preview)

Ve třetí části této třídílném kurzu budete nasazovat prediktivní model v R s Azure SQL Database Machine Learning Services (preview).

Vytvoříte uloženou proceduru s vložený skript R, který vytváří předpovědi pomocí modelu. Vzhledem k tomu, že váš model spouští ve službě Azure SQL database, můžete snadno školení pro data uložená v databázi.

V tomto článku se dozvíte jak:

> [!div class="checklist"]
> * Store prediktivního modelu v databázové tabulce
> * Vytvořit uloženou proceduru, která generuje modelu
> * Vytvořit uloženou proceduru, která vytváří předpovědi pomocí modelu
> * Spuštění modelem s novými daty

V [první část](sql-database-tutorial-predictive-model-prepare-data.md), jste zjistili, jak importovat ukázkovou databázi do Azure SQL database a poté si připravte data, která má být použit pro trénování modelu prediktivní v jazyce R.

V [druhá část](sql-database-tutorial-predictive-model-build-compare.md), jste zjistili, jak vytvořit a trénování modelů více a poté vyberte požadovanou možnost nejpřesnější.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Třetí části této série kurzů se předpokládá dokončení [ **první část** ](sql-database-tutorial-predictive-model-prepare-data.md) a [ **druhá část**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Vytvořit uloženou proceduru, která generuje modelu

Ve druhé části této série kurzů jste se rozhodli, bylo co nejvíce zpřesnili modelu rozhodovacího stromu (dtree). Teď Vytvořte uloženou proceduru (`generate_rental_rx_model`), trénovat a generuje dtree modelu s použitím rxDTree z balíčku RevoScaleR.

Spuštěním následujících příkazů v Azure Data studiu nebo v aplikaci SSMS.

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

## <a name="store-the-model-in-a-database-table"></a>Store modelu v databázové tabulce

Vytvoření tabulky v databázi databáze TutorialDB a pak model uložit do tabulky.

1. Vytvoření tabulky (`rental_rx_models`) pro ukládání modelu.

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

1. Uložte model do tabulky jako objekt binární s názvem "rxDTree model".

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Vytvořit uloženou proceduru, která vytváří předpovědi

Vytvoření uložené procedury (`predict_rentalcount_new`), která vytváří předpovědi pomocí trénovaný model a sadu nová data.

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

## <a name="execute-the-model-with-new-data"></a>Spuštění modelem s novými daty

Nyní můžete pomocí uložené procedury `predict_rentalcount_new` předpovědět počet pronájmu z nová data.

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

Zobrazí se výsledek podobný následujícímu.

```results
RentalCount_Predicted
332.571428571429
```

Jste úspěšně vytvořili, školení a nasazení modelu ve službě Azure SQL database. Pak jste použili tento model v uložené proceduře k předpovědi na základě hodnot na nová data.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi, pomocí databáze TutorialDB databáze, jej odstraníte z vašeho serveru Azure SQL Database.

Na webu Azure Portal postupujte podle těchto kroků:

1. V nabídce vlevo na webu Azure Portal vyberte **všechny prostředky** nebo **databází SQL**.
1. V **filtrovat podle názvu...**  zadejte **databáze TutorialDB**a vyberte své předplatné.
1. Vyberte vaši databázi databáze TutorialDB.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Ve třetí části této série kurzů dokončení těchto kroků:

* Store prediktivního modelu v databázové tabulce
* Vytvořit uloženou proceduru, která generuje modelu
* Vytvořit uloženou proceduru, která vytváří předpovědi pomocí modelu
* Spuštění modelem s novými daty

Další informace o používání R v Azure SQL Database Machine Learning Services (preview) najdete v tématu:

* [Zápis pokročilé funkce jazyka R ve službě Azure SQL Database pomocí služby Machine Learning (preview)](sql-database-machine-learning-services-functions.md)
* [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)
* [Přidat balíček R do Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-add-r-packages.md)
