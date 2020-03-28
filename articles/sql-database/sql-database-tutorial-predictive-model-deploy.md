---
title: 'Kurz: Nasazení prediktivního modelu v R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ve třetí části tohoto třídílného kurzu nasadíte prediktivní model v R se službami Machine Learning Services azure SQL Database (preview).
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
ms.openlocfilehash: 7779db053344f99238d38d5d49762730efbc5fc4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346326"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Kurz: Nasazení prediktivního modelu v R se službami Machine Learning Services azure SQL database (preview)

Ve třetí části tohoto třídílného kurzu nasadíte prediktivní model vyvinutý v R do databáze SQL pomocí služby Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Vytvoříte uloženou proceduru s vloženým skriptem R, který provádí předpovědi pomocí modelu. Vzhledem k tomu, že váš model se spustí v databázi Azure SQL, lze snadno trénovat proti datům uloženým v databázi.

V tomto článku se pomocí skriptů R, které jste vyvinuli v částech jedna a dvě, dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření uložené procedury, která generuje model strojového učení
> * Uložení modelu do databázové tabulky
> * Vytvořit uloženou proceduru, která umožňuje předpovědi pomocí modelu
> * Spuštění modelu s novými daty

V [první části](sql-database-tutorial-predictive-model-prepare-data.md)jste se naučili importovat ukázkovou databázi a potom připravit data, která mají být použita pro trénování prediktivního modelu v R.

Ve [druhé části](sql-database-tutorial-predictive-model-build-compare.md)jste se naučili vytvářet a trénovat více modelů strojového učení v R a pak zvolit nejpřesnější modely.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

* Třetí část této série kurzů předpokládá, že jste dokončili [**první**](sql-database-tutorial-predictive-model-prepare-data.md) a [**druhá část**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Vytvoření uložené procedury, která generuje model

Ve druhé části této série kurzů jste se rozhodli, že nejpřesnější byl model rozhodovacího stromu (dtree). Nyní pomocí skriptů R, které jste vyvinuli, vytvořte uloženou proceduru (`generate_rental_rx_model`), která trénuje a generuje model dtree pomocí rxDTree z balíčku RevoScaleR.

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

## <a name="store-the-model-in-a-database-table"></a>Uložení modelu do databázové tabulky

Vytvořte tabulku v databázi TutorialDB a uložte model do tabulky.

1. Vytvořte tabulku`rental_rx_models`( ) pro uložení modelu.

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Vytvořit uloženou proceduru, která umožňuje předpovědi

Vytvořte uloženou`predict_rentalcount_new`proceduru ( ), která umožňuje předpovědi pomocí trénovaného modelu a sady nových dat.

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

Nyní můžete použít uloženou proceduru `predict_rentalcount_new` k předvídání počtu pronájmů z nových dat.

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

Měli byste vidět výsledek podobný následujícímu.

```results
RentalCount_Predicted
332.571428571429
```

Úspěšně jste vytvořili, trénovali a nasadili model v databázi Azure SQL. Potom jste použili tento model v uložené proceduře předpovědět hodnoty na základě nových dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání databáze TutorialDB ji odstraňte z databázového serveru Azure SQL.

Na webu Azure Portal postupujte takto:

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** nebo **databáze SQL**.
1. Do pole **Filtr podle názvu...** zadejte **TutorialDB**a vyberte předplatné.
1. Vyberte databázi TutorialDB.
1. Na stránce **Přehled** vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve třetí části této série kurzů jste dokončili tyto kroky:

* Vytvoření uložené procedury, která generuje model strojového učení
* Uložení modelu do databázové tabulky
* Vytvořit uloženou proceduru, která umožňuje předpovědi pomocí modelu
* Spuštění modelu s novými daty

Další informace o používání jazyka R ve službě Azure SQL Database Machine Learning Services (preview) najdete v tématu:

* [Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
* [Práce s daty R a SQL ve službě Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)
* [Přidání balíčku R do služby Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-add-r-packages.md)
