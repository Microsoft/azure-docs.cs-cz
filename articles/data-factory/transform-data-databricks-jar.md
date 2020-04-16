---
title: Transformace dat pomocí nádoby Databricks Jar
description: Naučte se zpracovávat nebo transformovat data spuštěním databricks Jar.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414616"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformace dat spuštěním aktivity Jar v Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Azure Databricks Jar v [kanálu datové továrny](concepts-pipelines-activities.md) spouští Spark Jar ve vašem clusteru Azure Databricks. Tento článek vychází z článku [aktivit transformace](transform-data.md) dat, který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definice aktivity Databricks Jar

Zde je ukázka JSON definice Databricks Jar aktivity:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Vlastnosti aktivity Databricks Jar

Následující tabulka popisuje vlastnosti JSON použité v definici JSON:

|Vlastnost|Popis|Požaduje se|
|:--|---|:-:|
|jméno|Název aktivity v kanálu.|Ano|
|description|Text popisující, co aktivita dělá.|Ne|
|type|Pro Aktivita Databricks Jar je typem aktivity DatabricksSparkJar.|Ano|
|linkedServiceName|Název propojené služby Databricks, na kterém je spuštěna aktivita Jar. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) |Ano|
|název hlavní třídy|Úplný název třídy obsahující hlavní metodu, která má být provedena. Tato třída musí být obsažena v JAR poskytované jako knihovna.|Ano|
|parameters|Parametry, které budou předány hlavní metodě.  Toto je pole řetězců.|Ne|
|Knihovny|Seznam knihoven, které mají být nainstalovány v clusteru, který bude úlohu provádět. Může to být pole <řetězec, objekt>|Ano (alespoň jeden obsahující metodu mainClassName)|

> [!NOTE]
> **Známý problém** – při použití [stejného interaktivního clusteru](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) pro spouštění souběžných aktivit Databricks Jar (bez restartování clusteru) je známý problém v Databricks, kde v parametrech první aktivity budou použity také následující aktivity. Výsledkem je tedy nesprávné parametry, které jsou předávány následným úlohám. Chcete-li zmírnit toto místo toho použít [clusteru úloh.](compute-linked-services.md#example---using-new-job-cluster-in-databricks) 

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

Knihovny Jar jsou obvykle uloženy pod dbfs:/FileStore/jars při používání uživatelského uživatelského nastavení. Můžete seznam všech prostřednictvím CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopírování knihovny pomocí příkazového příkazu Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Použijte Databricks CLI [(instalační kroky)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Příklad - kopírování JAR do dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
