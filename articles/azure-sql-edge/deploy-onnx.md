---
title: Nasazení a vytvoření předpovědi pomocí ONNX
titleSuffix: SQL machine learning
description: Naučte se, jak vytvořit model, převést ho na ONNX, nasadit ho do Azure SQL Edge nebo Azure SQL Managed instance (Preview) a pak spustit nativní prediktivní zpracování dat pomocí nahraného modelu ONNX.
keywords: nasazení Edge SQL
ms.prod: sql
ms.technology: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 10/13/2020
ms.openlocfilehash: 510e9e2a9e90d62996ed6dcfebdd96f77b034f3d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000154"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-and-sql-machine-learning"></a>Nasazení a vytvoření předpovědi pomocí modelu ONNX a strojového učení SQL

V tomto rychlém startu se dozvíte, jak vytvořit model, převést ho na ONNX, nasadit ho do služby [Azure SQL Edge](onnx-overview.md) nebo [Azure SQL Managed instance (Preview)](../azure-sql/managed-instance/machine-learning-services-overview.md)a pak spustit nativní předpověď dat pomocí nahraného modelu ONNX.

Tento rychlý Start je založený na **scikit-učení** a používá [datovou sadu Boston](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html).

## <a name="before-you-begin"></a>Než začnete

* Pokud používáte Azure SQL Edge a nejste nasadili modul Azure SQL Edge, postupujte podle kroků v části [nasazení SQL Edge pomocí Azure Portal](deploy-portal.md).

* Nainstalujte [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download).

* Nainstalujte balíčky Pythonu potřebné pro tento rychlý Start:

  1. Otevřete [Nový Poznámkový blok](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks) připojený k jádru Python 3. 
  1. Klikněte na **Spravovat balíčky** .
  1. Na kartě **nainstalované** vyhledejte v seznamu nainstalovaných balíčků následující balíčky Pythonu. Pokud některý z těchto balíčků není nainstalován, vyberte kartu **Přidat novou** , vyhledejte balíček a klikněte na tlačítko **nainstalovat**.
     - **scikit-learn**
     - **numpy**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **setuptools**
     - **skl2onnx**
     - **sqlalchemy**

* U každého skriptu níže ho zadejte do buňky na poznámkovém bloku Azure Data Studio a pak buňku spusťte.

## <a name="train-a-pipeline"></a>Výuka kanálu

Rozdělit datovou sadu, aby používala funkce pro předpověď střední hodnoty domu.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Výstup**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Vytvořte kanál pro výuku modelu LinearRegression. Můžete také použít další regresní modely.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Zkontrolujte přesnost modelu a pak Vypočítejte výsledek R2 a střední chybu.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Výstup**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Převést model na ONNX

Převeďte datové typy na podporované datové typy SQL. Tento převod bude vyžadován i pro jiné datarámy.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Pomocí `skl2onnx` , převeďte LinearRegression model na formát ONNX a uložte ho místně.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Testování modelu ONNX

Po převedení modelu na formát ONNX je potřeba, aby se v modelu zobrazovala malá a žádná snížení výkonu.

> [!NOTE]
> ONNX runtime používá Floaty namísto Double, takže je možné malé nedostatky.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Výstup**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Vložení modelu ONNX

Uložte model do Azure SQL Edge nebo spravované instance Azure SQL v `models` tabulce v databázi `onnx` . V připojovacím řetězci zadejte **adresu serveru**, **uživatelské jméno**a **heslo**.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Načtení dat

Načtěte data do SQL.

Nejdřív vytvořte dvě tabulky, **funkce** a **cíl**a uložte podmnožiny datové sady Boston.

* **Funkce** obsahují všechna data, která se používají k předpovědi cíle a mediánu hodnoty. 
* **Cíl** obsahuje medián hodnoty pro každý záznam v datové sadě. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Nakonec použijte `sqlalchemy` k vložení `x_train` a `y_train` pandasí dataframes do tabulek a v `features` `target` uvedeném pořadí. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Nyní můžete zobrazit data v databázi.

## <a name="run-predict-using-the-onnx-model"></a>Spuštění PREDIKTIVNÍho použití modelu ONNX

Pomocí modelu v SQL spusťte nativní předpověď dat pomocí nahraného modelu ONNX.

> [!NOTE]
> Chcete-li spustit zbývající buňku, změňte jádro poznámkového bloku na SQL.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>Další kroky

* [Machine Learning a AI s ONNXem v SQL Edge](onnx-overview.md)
* [Machine Learning Services ve spravované instanci Azure SQL (Preview)](../azure-sql/managed-instance/machine-learning-services-overview.md)
