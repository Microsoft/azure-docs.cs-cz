---
title: Transformace dat s Databricks Pythonu – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat nebo transformovat data spuštěním Databricks Python.
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
ms.openlocfilehash: fa19ff6e6293e1d5d082ee40d55b456500fcb1ad
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075972"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformovat data spuštěním aktivity Python v Azure Databricks

Aktivity Azure Databricks Python v [kanálu služby Data Factory](concepts-pipelines-activities.md) soubor Pythonu běží v clusteru Azure Databricks. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

## <a name="databricks-python-activity-definition"></a>Definice aktivity Databricks Pythonu

Tady je ukázková definice JSON aktivity Python Databricks:

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

Následující tabulka popisuje vlastnostech JSON použitých v definici JSON:

|Vlastnost|Popis|Požaduje se|
|---|---|---|
|jméno|Název aktivity v kanálu.|Ano|
|description|Text popisující, jakým způsobem aktivita naloží.|Ne|
|type|Pro aktivitu Python Databricks typ aktivity je DatabricksSparkPython.|Ano|
|linkedServiceName|Název propojené služby Databricks, ve kterém se spouští aktivity Python. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku.|Ano|
|pythonFile|Identifikátor URI soubor Pythonu, který má být proveden. Jsou podporovány pouze DBFS cesty.|Ano|
|parameters|Parametry příkazového řádku, které se předají do souboru Python. Toto je pole řetězců.|Ne|
|Knihovny|Seznam knihovny, které chcete nainstalovat na clusteru, který provede úlohu. Pole může být < řetězec, objekt >|Ne|

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

Obvykle jsou uložené na soubor Jar knihovny pod dbfs: / funkce FileStore/jars při používání uživatelského rozhraní. Můžete zobrazit seznam všech přes rozhraní příkazového řádku: *databricks fs ls dbfs: / funkce FileStore/JAR* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Knihovna kopírování pomocí rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Příklad: *databricks fs cp SparkPi sestavení 0.1.jar dbfs: / funkce FileStore/JAR*