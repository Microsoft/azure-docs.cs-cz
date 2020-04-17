---
title: Vytvoření a trénování prediktivního modelu v R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Vytvořte jednoduchý prediktivní model v R pomocí Služby strojového učení Azure SQL Database Machine Learning Services (preview) a pak předpovědět výsledek pomocí nových dat.
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
ms.openlocfilehash: 3c88bdf141e7784837a89c8104574d97c93296dc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460150"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Úvodní příručka: Vytvoření a trénování prediktivního modelu v R se službami Machine Learning Services azure SQL database (preview)

V tomto rychlém startu vytvoříte a trénete prediktivní model pomocí R, uložíte model do tabulky v databázi a pak pomocí modelu předpovídáte hodnoty z nových dat pomocí služby Machine Learning Services (s R) v Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze Azure SQL](sql-database-single-database-get-started.md) s [pravidlem brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md)
- [Služby strojového učení](sql-database-machine-learning-services-overview.md) s povolenou r.
- [Sql Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Tento příklad používá jednoduchý regresní model k předvídání brzdné dráhy vozu na základě rychlosti pomocí datové sady **vozů,** která je součástí r.

> [!TIP]
> Mnoho datových sad jsou součástí runtime R, chcete-li `library(help="datasets")` získat seznam nainstalovaných datových sad, zadejte z příkazového řádku R.

## <a name="create-and-train-a-predictive-model"></a>Vytvoření a trénování prediktivního modelu

Údaje o rychlosti **vozidla** v datové sadě vozů obsahují dva sloupce, oba číselné: **dist** a **speed**. Data zahrnují více zastavení pozorování při různých rychlostech. Z těchto dat vytvoříte lineární regresní model, který popisuje vztah mezi rychlostí auta a vzdáleností potřebnou k zastavení auta.

Požadavky na lineární model jsou jednoduché:
- Definujte vzorec, který popisuje vztah mezi závislou proměnnou *rychlostí* a nezávislou proměnnou *vzdáleností*.
- Poskytnutí vstupních dat pro použití při trénování modelu.

> [!TIP]
> Pokud potřebujete opakovací postup u lineárních modelů, vyzkoušejte tento návod, který popisuje proces montáže modelu pomocí rxLinMod: [Montáž lineárních modelů](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

V následujících krocích nastavíte trénovací data, vytvoříte regresní model, natrénujete ho pomocí trénovacích dat a pak model uložíte do tabulky SQL.

1. Otevřete **SQL Server Management Studio** a připojte se k databázi SQL.

   Pokud potřebujete pomoc s připojením, přečtěte si [úvodní příručku: Pomocí aplikace SQL Server Management Studio se můžete připojit k databázi Azure SQL a zadat dotaz](sql-database-connect-query-ssms.md)na ni .

1. Vytvořte tabulku **CarSpeed** pro uložení trénovacích dat.

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

1. Vytvořte regresní `rxLinMod`model pomocí . 

   Chcete-li vytvořit model, definujete vzorec uvnitř kódu R a pak předáte trénovací data **CarSpeed** jako vstupní parametr.

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

1. Vytvořte tabulku, do které uložíte model, abyste ho později mohli použít pro předpověď. 

   Výstup balíčku R, který vytvoří model, je obvykle **binární objekt**, takže tabulka musí mít sloupec typu **VARBINARY(max).**

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Nyní volání uložené procedury, generovat model a uložit do tabulky.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Všimněte si, že když tento kód spustíte podruhé, zobrazí se tato chyba:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Jednou z možností, jak se této chybě vyhnout, je aktualizovat název každého nového modelu. Můžete například použít popisnější název a zahrnout do něj typ modelu, den vytvoření atd.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Zobrazit tabulku koeficientů

Obecně platí, že výstup kódu R z uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) je omezený na jeden datový rámec. Kromě datového rámce však můžete vracet i výstupy jiných typů, například skaláry.

Předpokládejme například, že chcete trénovat model, ale okamžitě zobrazit tabulku koeficientů z modelu. Chcete-li tak učinit, vytvořte tabulku koeficientů jako hlavní sadu výsledků a výstup trénovaného modelu v proměnné SQL. Model můžete okamžitě znovu použít voláním proměnné, nebo můžete model uložit do tabulky, jak je znázorněno zde.

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

**Výsledky**

![Natrénovaný model s dalším výstupem](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Skóre nových dat pomocí trénovaného modelu

*Vyhodnocování* je termín používaný v datové vědě znamenat generování předpovědi, pravděpodobnosti nebo jiné hodnoty založené na nových dat do trénovaného modelu. Model, který jste vytvořili v předchozí části, použijete k porovnání s novými daty.

Všimli jste si, že původní trénovací data končila rychlostí 25 MPH? Je to proto, že původní data vycházela z experimentu z roku 1920. Možná se divíte, jak dlouho by trvalo automobil u 1920 zastavit, pokud by to mohlo jít tak rychle, jak 60 mph nebo dokonce 100 mph? Chcete-li odpovědět na tuto otázku, můžete zadat některé nové hodnoty rychlosti modelu.

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

2. Předpovědět brzdnou dráhu z těchto nových hodnot rychlosti.

   Vzhledem k tomu, že váš model je založen na algoritmu **rxLinMod** poskytované jako součást balíčku **RevoScaleR,** zavoláte funkci [rxPredict,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) nikoli obecnou funkci R. `predict`

   Tento ukázkový skript:
   - Používá příkaz SELECT k získání jednoho modelu z tabulky.
   - Předá jej jako vstupní parametr
   - Volá `unserialize` funkci na modelu
   - Aplikuje `rxPredict` funkci s příslušnými argumenty na model.
   - Poskytuje nová vstupní data

   > [!TIP]
   > Vyhodnocování v reálném čase najdete [v tématu Serializace funkce](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) poskytované RevoScaleR.

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

   **Výsledky**

   ![Sada výsledků dotazu pro předpověď brzdné dráhy](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> V tomto příkladskriptu `str` funkce je přidánběhem fáze testování ke kontrole schématu dat vrácených z R. Příkaz můžete odebrat později.
>
> Názvy sloupců použité ve skriptu R není nutné předávat do výstupu uložené procedury. Zde s výsledky klauzule definuje některé nové názvy sloupců.

## <a name="next-steps"></a>Další kroky

Další informace o službách Azure SQL Database Machine Learning Services s R (preview) najdete v následujících článcích.

- [Služby strojového učení azure SQL database s r (preview)](sql-database-machine-learning-services-overview.md)
- [Vytváření a spouštění jednoduchých skriptů R ve službách Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)
- [Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL ve službě Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)
