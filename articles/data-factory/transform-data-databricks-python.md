---
title: Transformace dat pomocí Databricks Python
description: Naučte se zpracovávat nebo transformovat data spuštěním Databricks Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.openlocfilehash: be2e389a0f103983a566a3f74d201e5589d84586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926722"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformace dat spuštěním aktivity Pythonu v Azure Databricks

Aktivita Pythonu Azure Databricks v [kanálu datové továrny](concepts-pipelines-activities.md) spouští soubor Pythonu v clusteru Azure Databricks. Tento článek vychází z článku [aktivit transformace](transform-data.md) dat, který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definice aktivity Databricks Python

Zde je ukázka JSON definice Databricks Python aktivity:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Vlastnosti aktivity Databricks Pythonu

Následující tabulka popisuje vlastnosti JSON použité v definici JSON:

|Vlastnost|Popis|Požaduje se|
|---|---|---|
|jméno|Název aktivity v kanálu.|Ano|
|description|Text popisující, co aktivita dělá.|Ne|
|type|Pro Databricks Python Activity je typem aktivity DatabricksSparkPython.|Ano|
|linkedServiceName|Název propojené služby Databricks, na které je spuštěna aktivita Pythonu. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) |Ano|
|pythonFile|Identifikátor URI souboru Pythonu, který má být proveden. Podporovány jsou pouze cesty DBFS.|Ano|
|parameters|Parametry příkazového řádku, které budou předány souboru Pythonu. Toto je pole řetězců.|Ne|
|Knihovny|Seznam knihoven, které mají být nainstalovány v clusteru, který bude úlohu provádět. Může to být pole <řetězec, objekt>|Ne|

## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity datových cihel

Ve výše uvedené definici aktivity Databricks zadáte tyto typy knihoven: *jar*, *egg*, *maven*, *pypi*, *cran*.

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

Další podrobnosti naleznete v [dokumentaci databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) pro typy knihoven.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak nahrát knihovnu v Databricks

#### <a name="using-databricks-workspace-ui"></a>[Použití uj.](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat dbfs cestu knihovny přidané pomocí ui, můžete použít [Databricks CLI (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Knihovny Jar jsou obvykle uloženy pod dbfs:/FileStore/jars při používání uživatelského uživatelského nastavení. Můžete seznam všech prostřednictvím CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopírování knihovny pomocí příkazového příkazu Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Příklad: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*