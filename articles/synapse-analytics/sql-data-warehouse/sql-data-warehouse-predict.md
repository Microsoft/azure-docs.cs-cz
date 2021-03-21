---
title: Skóre modelů strojového učení s předpovídat
description: Naučte se, jak pomocí funkce PREDIKTIVNÍho jazyka T-SQL ve vyhrazeném fondu SQL určit skóre modelů strojového učení.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9e7d45a588e60cd082f1eef43d1d1b6681b9e912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117737"
---
# <a name="score-machine-learning-models-with-predict"></a>Skóre modelů strojového učení s předpovídat

Vyhrazený fond SQL nabízí možnost skóre modelů strojového učení pomocí známého jazyka T-SQL. Pomocí jazyka T- [SQL můžete](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)přenést stávající modely strojového učení s historickými daty a určit jejich skóre v rámci zabezpečených hranic datového skladu. Funkce PREDIKTIVNÍho přebírá model [ONNX (Open neuronové Network Exchange)](https://onnx.ai/) a data jako vstupy. Tato funkce eliminuje krok přesunu cenných dat mimo datový sklad pro účely bodování. Cílem je umožnit odborníkům v oblasti dat snadno nasadit modely strojového učení pomocí známého rozhraní T-SQL a zároveň bez problémů spolupracovat s odborníky přes data, kteří pracují se správnou architekturou pro jejich úkoly.

> [!NOTE]
> Tato funkce není v současnosti podporovaná v SQL fondu bez serveru.

Tato funkce vyžaduje, aby byl model vyškolený mimo synapse SQL. Jakmile model sestavíte, načtěte ho do datového skladu a Zastavte ho pomocí syntaxe pro předpověď T-SQL, abyste získali přehledy z dat.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>Školení modelu

Vyhrazený fond SQL očekává předem vyškolený model. Při školení modelu strojového učení, který se používá k provádění předpovědi ve vyhrazeném fondu SQL, mějte na paměti následující faktory.

- Vyhrazený fond SQL podporuje jenom modely formátu ONNX. ONNX je open source formát modelu, který umožňuje výměnu modelů mezi různými architekturami, aby bylo možné vzájemnou funkční spolupráci. Stávající modely můžete převést do formátu ONNX pomocí platforem, které ji buď nativně podporují, nebo převádění dostupných balíčků. Například balíček [skriptu sklearn-Onnx](https://github.com/onnx/sklearn-onnx) převádí modely scikit-učení na Onnx. [Úložiště GitHub ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format) nabízí seznam podporovaných rozhraní a příkladů.

   Pokud používáte [automatizované ml](../../machine-learning/concept-automated-ml.md) pro školení, nezapomeňte nastavit parametr *enable_onnx_compatible_models* na hodnotu true, aby se vytvořil model formátu Onnx. [Automatizovaný Machine Learning Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) ukazuje příklad použití automatizovaného ml k vytvoření modelu strojového učení formátu ONNX.

- Pro vstupní data jsou podporovány následující datové typy:
    - int, bigint, Real, float
    - char, varchar, nvarchar

- Data bodování musí být ve stejném formátu jako školicí data. PŘEDPOVĚDI nepodporují komplexní datové typy, jako například multidimenzionální pole. Takže pro účely školení se ujistěte, že každý vstup modelu odpovídá jednomu sloupci tabulky bodování místo předání jednoho pole obsahujícího všechny vstupy.

- Ujistěte se, že názvy a datové typy vzorových vstupů odpovídají názvům sloupců a datovým typům nových dat předpovědi. Vizualizace modelu ONNX pomocí různých open-source nástrojů dostupných online může další pomoc s laděním.

## <a name="loading-the-model"></a>Načítání modelu

Model je uložený v uživatelské tabulce vyhrazeného fondu SQL jako šestnáctkový řetězec. Další sloupce, například ID a popis, mohou být přidány v tabulce modelů pro identifikaci modelu. Jako datový typ sloupce modelu použijte varbinary (max). Zde je příklad kódu pro tabulku, která se dá použít pro ukládání modelů:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Po převodu modelu na šestnáctkový řetězec a zadanou definici tabulky použijte [příkaz Kopírovat](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest) nebo základ pro načtení modelu do vyhrazené tabulky fondu SQL. Následující ukázka kódu používá k načtení modelu příkaz Copy.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>Bodování modelu

Jakmile se model a data načtou do datového skladu, použijte k vyhodnocení modelu funkci **předpověď T-SQL** . Ujistěte se, že nová vstupní data jsou ve stejném formátu jako školicí data použitá k vytvoření modelu. Předpověď T-SQL přebírá dva vstupy: model a nové vstupní data bodování a generuje nové sloupce pro výstup. Model lze zadat jako proměnnou, literál nebo skalární sub_query. Použijte [s common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest) k určení pojmenované sady výsledků pro datový parametr.

Následující příklad ukazuje vzorový dotaz pomocí funkce předpovědi. Vytvoří se další sloupec s názvem *skóre* a datovým typem *float* , který obsahuje výsledky předpovědi. Všechny sloupce vstupních dat a výstupní prediktivní sloupce jsou k dispozici pro zobrazení pomocí příkazu SELECT. Další podrobnosti najdete v tématu [předpověď (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Další kroky

Další informace o funkci předpověď naleznete v tématu [předpověď (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).