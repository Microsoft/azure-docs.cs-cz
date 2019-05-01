---
title: Vytvoření a natrénujeme prediktivní model v jazyce R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Vytvořte jednoduchým prediktivním modelem v jazyk R s využitím Azure SQL Database Machine Learning Services (preview) a potom předpovědět výsledek pomocí nová data.
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
ms.openlocfilehash: c1719064de53b79a127146d0ab034f461657cc64
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714893"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Vytvoření a natrénujeme prediktivní model v R s Azure SQL Database Machine Learning Services (preview)

V tomto rychlém startu budete vytvářet a natrénujeme prediktivní model pomocí jazyka R, uložit model do tabulky v databázi SQL pak použít model k předpovědi hodnot z nových dat pomocí verze public preview [Machine Learning Services (s jazykem R) ve službě Azure SQL Database ](sql-database-machine-learning-services-overview.md). 

Model, který budete používat v tomto rychlém startu je jednoduchý regresní model předpovídající vzdálenost zastavení automobilu podle rychlosti. Budete používat **auta** datovou sadu zahrnutou s jazykem R, protože je malý a snadno pochopitelná.

> [!TIP]
> Součástí modulu runtime R je řada malých i velkých datových sad. Pokud chcete získat seznam datových sad nainstalované s jazykem R, zadejte `library(help="datasets")` z příkazového řádku jazyka R.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

- Chcete-li spustit ukázkový kód v těchto cvičeních, musíte nejprve mít databázi Azure SQL Machine Learning Services (R) povolena. Ve verzi public preview, společnost Microsoft bude připojit je a povolit strojového učení pro existující nebo nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spustit skripty R používat správu jiných databází nebo nástrojů pro dotazování, ale v tomto rychlém startu budete používat SSMS.

- Tento rychlý start vyžaduje nakonfigurovat pravidlo brány firewall na úrovni serveru. Informace o tom, jak to provést, najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Vytvoření a natrénujeme prediktivní model

Data o rychlosti car v **auta** datová sada obsahuje dva sloupce, číselné: **dist** a **rychlost**. Data obsahují více pozorování zastavení při různých rychlostech. Z těchto dat vytvoříte model lineární regrese, který popisuje relace mezi rychlostí car a vzdálenost potřebné k zastavení automobilu.

Požadavky na lineární model jsou jednoduché:
- Definovat vzorec, který popisuje relace mezi závislé proměnné *rychlost* a nezávislé proměnné *vzdálenost*.
- Poskytnutí vstupních dat pro použití při trénování modelu.

> [!TIP]
> Pokud potřebujete občerstvit lineární modelů, vyzkoušejte tento výukový kurz, který popisuje postup přizpůsobení modelu pomocí rxLinMod: [Přizpůsobení lineární modelů](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

V následujících krocích, které nastavíte trénovací data vytvořte regresní model, trénování pomocí trénovacích dat a pak model uložte na tabulku SQL.

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomoc s připojením, přečtěte si [rychlý start: Pomocí SQL Server Management Studio k připojení a dotazování Azure SQL database](sql-database-connect-query-ssms.md).

1. Vytvořte **CarSpeed** tabulky trénovací data uložte pomocí.

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

1. Vytvoření s použitím regresní model `rxLinMod`. 

   K sestavení modelu definovat vzorec uvnitř kód R a pak předejte trénovacích dat **CarSpeed** jako vstupní parametr.

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

1. Vytvořte tabulku, kam se ukládají modelu, můžete ho použít později pro předpovědi. 

   Výstup balíčku R, který vytvoří model je obvykle **binární objekt**, takže se tabulka musí obsahovat sloupec **VARBINARY(max)** typu.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Nyní volání uložené procedury, generování modelu a uložte ho do tabulky.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Všimněte si, že když tento kód spustíte podruhé, zobrazí se tato chyba:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Jednou z možností lze vyvarovat této chyby je název pro každý nový model aktualizace. Můžete například použít popisnější název a zahrnout do něj typ modelu, den vytvoření atd.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Podívejte se do tabulky koeficienty

Obecně platí, že výstup kódu R z uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) je omezený na jeden datový rámec. Kromě datového rámce však můžete vracet i výstupy jiných typů, například skaláry.

Předpokládejme například, že chcete trénování modelu, ale v tabulce koeficienty z modelu se zobrazí okamžitě. Uděláte to tak, vytvoření tabulky koeficienty jako hlavní výsledek nastavit a výstup trénovaného modelu do proměnné SQL. Můžete okamžitě znovu použít model voláním proměnné nebo modelu můžete uložit do tabulky, jak je znázorněno zde.

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

**Results**

![Natrénovaný model s dalším výstupem](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Stanovení skóre nových dat pomocí trénovaného modelu

*Vyhodnocování* je termín používaný v pro datovou vědu jejich generování predikcí pravděpodobnosti nebo jiné hodnoty na základě nových dat do trénovaného modelu. Použijete model, který jste vytvořili v předchozí části ke stanovení skóre pro nová data předpovědi.

Všimli jste si, že původní trénovací data končila rychlostí 25 MPH? Je to proto, že původní data vycházela z experimentu z roku 1920. Může vás zajímat, jak dlouho bude trvat automobilu představuje jeden z 1920s zastavit, pokud ho mohli dát do toho stejně rychle jako 60 mph nebo dokonce 100 mph? Na tuto otázku odpovědět, můžete zadat některé nové hodnoty rychlost do modelu.

1. Vytvořte tabulku s novými daty rychlost.

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

2. Zastavení vzdálenost od tyto nové hodnoty rychlosti Předvídejte.

   Vzhledem k tomu, že je na základě modelu **rxLinMod** algoritmus poskytnutého jako součást **RevoScaleR** balíčku, volání [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) funkce namísto obecného R `predict` funkce.

   Tento ukázkový skript:
   - Příkaz SELECT používá k získání modelu jediné z tabulky
   - Předá jej jako vstupní parametr
   - Volání `unserialize` funkce na modelu
   - Se vztahuje `rxPredict` funkce s příslušnými argumenty do modelu
   - Poskytuje nové vstupní data

   > [!TIP]
   > Vyhodnocování v reálném čase, naleznete v tématu [serializace funkce](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) poskytované RevoScaleR.

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

   **Results**

   ![Sada výsledků dotazu pro předpověď brzdné dráhy](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> V tomto příkladu skriptu `str` funkce se přidá během fáze testování zkontrolujte schéma dat se vrací z R. Můžete odebrat příkaz později.
>
> Názvy sloupců použité ve skriptu R není nutné předávat do výstupu uložené procedury. Klauzule výsledku se tady definuje některé nové názvy sloupců.

## <a name="next-steps"></a>Další postup

Další informace o Azure SQL Database služby Machine Learning s jazykem R (preview) naleznete v následujících článcích.

- [Azure SQL Database služby Machine Learning s jazykem R (preview)](sql-database-machine-learning-services-overview.md)
- [Vytvořit a spustit jednoduché skripty jazyka R v Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)
- [Zápis pokročilé funkce jazyka R ve službě Azure SQL Database pomocí služby Machine Learning (preview)](sql-database-machine-learning-services-functions.md)
- [Práce s daty R a SQL v Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md)