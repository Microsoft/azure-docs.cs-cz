---
title: Vytvoření a výuka prediktivního modelu v jazyce R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Vytvořte v R jednoduché prediktivní modely pomocí Azure SQL Database Machine Learning Services (Preview) a pak předpověď výsledku pomocí nových dat.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 37cc50a31f2f31c0cb7fc49102328f2072c6570a
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324585"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Rychlý Start: vytvoření a výuka prediktivního modelu v R s Azure SQL Database Machine Learning Services (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto rychlém startu vytvoříte a provedete prediktivní model pomocí jazyka R, uložíte model do tabulky v databázi a potom použijete model k předpovědi hodnot z nových dat pomocí Machine Learning Services (s R) v Azure SQL Database.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze v Azure SQL Database](single-database-create-quickstart.md) s [pravidlem brány firewall na úrovni serveru](firewall-create-server-level-portal-quickstart.md)
- [Machine Learning Services](machine-learning-services-overview.md) s povoleným R.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

V tomto příkladu se používá jednoduchý regresní model k předpovídání brzdné dráhy auta na základě rychlosti pomocí datové sady **automobilů** zahrnuté v jazyce R.

> [!TIP]
> K dispozici je řada datových sad s modulem runtime R. Pokud chcete získat seznam nainstalovaných datových sad, zadejte na `library(help="datasets")` příkazovém řádku r.

## <a name="create-and-train-a-predictive-model"></a>Vytvoření a výuka prediktivního modelu

Data rychlosti auta v datové sadě **automobilů** obsahují dva sloupce, jak číselné: **DIST** , tak i **Speed**. Data obsahují více zastavování pozorování s různou rychlostí. Z těchto dat vytvoříte model lineární regrese, který popisuje vztah mezi rychlostí auta a vzdáleností nutnou k zastavení auta.

Požadavky na lineární model jsou jednoduché:
- Definujte vzorec, který popisuje vztah mezi proměnnou závislých proměnných a *vzdáleností* *nezávislé proměnné.*
- Poskytnutí vstupních dat pro použití při trénování modelu.

> [!TIP]
> Pokud potřebujete aktualizační program pro lineární modely, vyzkoušejte tento kurz, který popisuje proces přizpůsobení modelu pomocí rxLinMod: [přizpůsobení lineárních modelů](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

V následujících krocích nastavíte školicí údaje, vytvoříte regresní model, provedete ho pomocí školicích dat a pak uložíte model do tabulky SQL.

1. Otevřete **SQL Server Management Studio** a připojte se k databázi.

   Pokud potřebujete pomáhat s připojením, přečtěte si téma [rychlý Start: použití SQL Server Management Studio k připojení a dotazování databáze v Azure SQL Database](connect-query-ssms.md).

1. Vytvořte tabulku **CarSpeed** k uložení školicích dat.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Vytvořte regresní model pomocí `rxLinMod` . 

   Chcete-li vytvořit model, který definujete v kódu R, a pak předejte školicí data **CarSpeed** jako vstupní parametr.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     První argument funkce rxLinMod je parametr *formula*, který definuje závislost proměnné distance (vzdálenost) na proměnné speed (rychlost). Vstupní data jsou uložená v proměnné `CarsData`, která se naplní dotazem SQL.

1. Vytvořte tabulku, do které model uložíte, abyste ho mohli později použít pro předpověď. 

   Výstupem balíčku R, který vytváří model, je obvykle **binární objekt**, takže tabulka musí mít sloupec typu **varbinary (max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Nyní zavolejte uloženou proceduru, vygenerujte model a uložte ji do tabulky.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Všimněte si, že když tento kód spustíte podruhé, zobrazí se tato chyba:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Jednu z možností, jak se vyhnout této chybě, je aktualizovat název pro každý nový model. Můžete například použít popisnější název a zahrnout do něj typ modelu, den vytvoření atd.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Zobrazit tabulku koeficientů

Obecně platí, že výstup kódu R z uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) je omezený na jeden datový rámec. Kromě datového rámce však můžete vracet i výstupy jiných typů, například skaláry.

Předpokládejme například, že chcete vytvořit výuku modelu, ale hned zobrazit tabulku koeficientů z modelu. K tomu je třeba vytvořit tabulku koeficientů jako hlavní sadu výsledků a výstupem vyškolený model v proměnné SQL. Model můžete okamžitě znovu použít voláním proměnné, nebo můžete model Uložit do tabulky, jak je znázorněno zde.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Důsledk**

![Natrénovaný model s dalším výstupem](./media/r-train-score-model-create-quickstart/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Hodnocení nových dat pomocí trained model

*Bodování* je termín používaný v datové vědy k vytvoření předpovědi, pravděpodobnosti nebo dalších hodnot na základě nových dat, která jsou vydávána do trained model. Pomocí modelu, který jste vytvořili v předchozí části, můžete určit skóre předpovědi proti novým datům.

Všimli jste si, že původní trénovací data končila rychlostí 25 MPH? Je to proto, že původní data vycházela z experimentu z roku 1920. Můžete se setkat s tím, jak dlouho by se od 1920s dostal automobil, pokud by mohl trvat až 60 mph nebo dokonce 100 mph? K zodpovězení této otázky můžete pro svůj model zadat nějaké nové hodnoty rychlosti.

1. Vytvořte tabulku s novými daty o rychlosti.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Předpovídání zastavování vzdálenosti z těchto nových hodnot rychlosti.

   Vzhledem k tomu, že váš model je založen na algoritmu **rxLinMod** , který je součástí balíčku **RevoScaleR** , je místo obecné funkce jazyka R volána funkce [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) `predict` .

   Tento ukázkový skript:
   - Pomocí příkazu SELECT získá jeden model z tabulky.
   - Předá ho jako vstupní parametr.
   - Volá `unserialize` funkci na modelu.
   - Použije `rxPredict` funkci s příslušnými argumenty pro model.
   - Poskytuje nová vstupní data.

   > [!TIP]
   > Pro bodování v reálném čase naleznete informace v tématu [serializace Functions](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) , které poskytuje RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Důsledk**

   ![Sada výsledků dotazu pro předpověď brzdné dráhy](./media/r-train-score-model-create-quickstart/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> V tomto příkladu skriptu `str` je funkce přidána během testovací fáze pro kontrolu schématu dat vrácených z R. Příkaz můžete odebrat později.
>
> Názvy sloupců použité ve skriptu R není nutné předávat do výstupu uložené procedury. Tady je klauzule WITH RESULTs definující některé nové názvy sloupců.

## <a name="next-steps"></a>Další kroky

Další informace o Azure SQL Database Machine Learning Services s R (Preview) najdete v následujících článcích.

- [Azure SQL Database Machine Learning Services s R (Preview)](machine-learning-services-overview.md)
- [Vytvoření a spuštění jednoduchých skriptů R v Azure SQL Database Machine Learning Services (Preview)](r-script-create-quickstart.md)
- [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](machine-learning-services-functions.md)
- [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (Preview)](machine-learning-services-data-issues.md)
