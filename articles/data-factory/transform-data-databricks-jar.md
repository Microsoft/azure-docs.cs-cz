---
title: Transformace dat s Databricks Jar – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat nebo transformovat data spuštěním Databricks Jar.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: a47d0130cd06a936da456ec6d78bde99907072f2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526296"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformovat data spuštěním Jar aktivit v Azure Databricks

Jar aktivit Azure Databricks v [kanálu služby Data Factory](concepts-pipelines-activities.md) spuštění Sparku Jar v clusteru Azure Databricks. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definice aktivity Databricks Jar

Tady je ukázková definice JSON aktivity Databricks Jar:

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

Následující tabulka popisuje vlastnostech JSON použitých v definici JSON:

|Vlastnost|Popis|Požaduje se|
|:--|---|:-:|
|jméno|Název aktivity v kanálu.|Ano|
|description|Text popisující, jakým způsobem aktivita naloží.|Ne|
|type|Pro aktivitu Jar Databricks je typ aktivity DatabricksSparkJar.|Ano|
|linkedServiceName|Název propojené služby Databricks, ve kterém se spouští aktivity soubor Jar. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku.|Ano|
|mainClassName|Úplný název třídy obsahující metodu main má být proveden. Tato třída musí být součástí JAR ve formě knihovny.|Ano|
|parameters|Parametry, které se předají do metody main.  Toto je pole řetězců.|Ne|
|Knihovny|Seznam knihovny, které chcete nainstalovat na clusteru, který provede úlohu. Pole může být < řetězec, objekt >|Ano (alespoň jeden obsahující metodu mainClassName)|

## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity databricks

Ve výše uvedené definice aktivity Databricks zadáte tyto knihovny typů: *jar*, *egg*, *maven*, *pypi*,  *cran*.

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
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

Další informace naleznete v [dokumentace k Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) knihovny typů.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak nahrát knihovnu v Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Používání pracovního prostoru Databricks uživatelského rozhraní](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat cestu dbfs knihovny přidat pomocí uživatelského rozhraní, můžete použít [rozhraní příkazového řádku Databricks (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Obvykle jsou uložené na soubor Jar knihovny pod dbfs: / funkce FileStore/jars při používání uživatelského rozhraní. Můžete zobrazit seznam všech přes rozhraní příkazového řádku: *databricks fs ls dbfs: / funkce FileStore/úloha JAR* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Knihovna kopírování pomocí rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Použití rozhraní příkazového řádku Databricks [(postup instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Příklad: kopírování JAR dbfs: *dbfs cp SparkPi sestavení 0.1.jar dbfs:/docs/sparkpi.jar*
