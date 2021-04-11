---
title: 'Rychlý Start: čtení dat z ADLS Gen2 do PANDAS dataframe'
description: Načte data z účtu Azure Data Lake Storage Gen2 do PANDAS dataframe pomocí Pythonu v synapse studiu ve službě Azure synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969863"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Rychlý Start: čtení dat z ADLS Gen2 do PANDAS dataframe v Azure synapse Analytics

V tomto rychlém startu se dozvíte, jak snadno pomocí Pythonu číst data z Azure Data Lake Storage (ADLS) Gen2 do PANDAS dataframe v Azure synapse Analytics.

Z poznámkového bloku synapse Studio:

- připojení ke kontejneru v Data Lake Storage Gen2, který je propojený s vaším pracovním prostorem Azure synapse Analytics
- Přečtěte si data z poznámkového bloku PySpark pomocí `spark.read.load`
- převést data na PANDAS dataframe pomocí `.toPandas()`

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
- Pracovní prostor analýzy synapse s Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště – musíte být **přispěvatelem dat objektů BLOB úložiště** Data Lake Storage Gen2 systému souborů, se kterým pracujete. Podrobnosti o tom, jak vytvořit pracovní prostor, najdete v tématu [Vytvoření pracovního prostoru synapse](get-started-create-workspace.md).
- Fond Apache Spark ve vašem pracovním prostoru – Přečtěte si téma [Vytvoření fondu Apache Spark bez serveru](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Nahrání ukázkových dat do ADLS Gen2

1. V Azure Portal vytvořte kontejner ve stejném Data Lake Storage Gen2 použitém v synapse studiu. Tento krok můžete přeskočit, pokud chcete použít výchozí propojený účet úložiště v pracovním prostoru Azure synapse Analytics.

1. V synapse studiu klikněte na **data**, vyberte **propojenou** kartu a vyberte kontejner pod **Azure Data Lake Storage Gen2**.

1. Stáhněte si ukázkový soubor [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) a nahrajte ho do kontejneru.

1. Vyberte nahraný soubor, klikněte na **vlastnosti** a zkopírujte hodnotu **cesty ABFSS** .

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Čtení dat z ADLS Gen2 do PANDAS dataframe

1. V levém podokně klikněte na tlačítko **vývoj**.

1. Klikněte **+** a vyberte Poznámkový blok pro vytvoření nového poznámkového bloku.

1. V oblasti **připojit k** vyberte fond Apache Spark. Pokud ho nemáte, klikněte na **vytvořit fond Apache Spark**.

1. Do buňky kódu poznámkového bloku vložte následující kód Pythonu, a to vložením cesty ABFSS, kterou jste zkopírovali dříve:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Spusťte buňku.

Po několika minutách by zobrazený text vypadal podobně jako v následujícím příkladu.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Další kroky

- [Co je Azure Synapse Analytics?](overview-what-is.md)
- [Začínáme s Azure synapse Analytics](get-started.md)
- [Vytvoření fondu Apache Spark bez serveru](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
