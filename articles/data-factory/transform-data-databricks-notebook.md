---
title: Transformace dat pomocí poznámkového bloku Databricks
description: Naučte se zpracovávat nebo transformovat data spuštěním poznámkového bloku Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419013"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformace dat spuštěním poznámkového bloku Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita poznámkového bloku Azure Databricks v [kanálu datové továrny](concepts-pipelines-activities.md) spouští poznámkový blok Databricks v pracovním prostoru Azure Databricks. Tento článek vychází z článku [aktivit transformace](transform-data.md) dat, který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definice aktivity poznámkového bloku Databricks

Zde je ukázka JSON definice Databricks notebook aktivity:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Vlastnosti aktivity poznámkového bloku Databricks

Následující tabulka popisuje vlastnosti JSON použité v definici JSON:

|Vlastnost|Popis|Požaduje se|
|---|---|---|
|jméno|Název aktivity v kanálu.|Ano|
|description|Text popisující, co aktivita dělá.|Ne|
|type|Pro Databricks Zápisník aktivity, typ aktivity je DatabricksNotebook.|Ano|
|linkedServiceName|Název propojené služby Databricks, na které je spuštěn poznámkový blok Databricks. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) |Ano|
|poznámkový blokPath|Absolutní cesta poznámkového bloku, který má být spuštěn v pracovním prostoru Databricks. Tato cesta musí začínat lomítkem.|Ano|
|baseParameters|Pole párů Klíč hodnota. Základní parametry lze použít pro každou spuštěnou aktivitu. Pokud poznámkový blok převezme parametr, který není zadán, použije se výchozí hodnota z poznámkového bloku. Další informace o parametrech naleznete v [poznámkových blocích Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Ne|
|Knihovny|Seznam knihoven, které mají být nainstalovány v clusteru, který bude úlohu provádět. Může to být \<pole řetězce, objekt>.|Ne|


## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity Databricks

Ve výše uvedené definici aktivity Databricks zadáte tyto typy knihoven: *jar*, *egg*, *whl*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Další podrobnosti naleznete v [dokumentaci Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) pro typy knihoven.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Předávání parametrů mezi notebooky a datovou továrnou

Parametry datové továrny můžete předat poznámkovým blokům pomocí vlastnosti *baseParameters* v aktivitě databricks. 

V některých případech můžete vyžadovat předání určitých hodnot z poznámkového bloku zpět do datové továrny, které lze použít pro tok řízení (podmíněné kontroly) v datové továrně nebo spotřebovávat navazující aktivity (limit velikosti je 2 MB). 

1. V poznámkovém bloku můžete volat [dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) a odpovídající "returnValue" budou vráceny do datové továrny.

2. Výstup v datové továrně můžete spotřebovat `'@activity('databricks notebook activity name').output.runOutput'`pomocí výrazu, například . 

   > [!IMPORTANT]
   > Pokud předáváte objekt JSON, můžete načíst hodnoty připojením názvů vlastností. Příklad: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Jak nahrát knihovnu v Databricks

#### <a name="using-databricks-workspace-ui"></a>[Použití uj.](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat dbfs cestu knihovny přidané pomocí ui, můžete použít [Databricks CLI (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Knihovny Jar jsou obvykle uloženy pod dbfs:/FileStore/jars při použití uživatelského uživatelského nastavení. Můžete seznam všech prostřednictvím CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Kopírování knihovny pomocí příkazového příkazu Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Příklad: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
