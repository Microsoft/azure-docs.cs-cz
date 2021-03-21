---
title: Transformace dat pomocí Pythonu datacihlů
description: Naučte se zpracovávat nebo transformovat data spuštěním aktivity Python datacihly v kanálu Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373942"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformujte data spuštěním aktivity Pythonu v Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Azure Databricks Pythonu v [kanálu Data Factory](concepts-pipelines-activities.md) spustí v clusteru Azure Databricks soubor Pythonu. Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definice aktivity v Pythonu datacihly

Tady je ukázková definice JSON aktivity datacihly v Pythonu:

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

## <a name="databricks-python-activity-properties"></a>Vlastnosti aktivity Pythonu datacihly

Následující tabulka obsahuje popis vlastností JSON použitých v definici JSON:

|Vlastnost|Popis|Vyžadováno|
|---|---|---|
|name|Název aktivity v kanálu.|Yes|
|description|Text popisující, co aktivita dělá.|No|
|typ|Pro aktivitu Python datacihly je typ aktivity DatabricksSparkPython.|Yes|
|linkedServiceName|Název propojené služby datacihly, na které běží aktivita Pythonu Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) .|Yes|
|pythonFile|Identifikátor URI souboru Pythonu, který má být spuštěn. Jsou podporovány pouze DBFS cesty.|Yes|
|parameters|Parametry příkazového řádku, které budou předány souboru Python. Toto je pole řetězců.|No|
|knihovny|Seznam knihoven, které se mají nainstalovat na cluster, který spustí úlohu. Může to být pole <řetězec, objekt>|No|

## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity datacihly

Ve výše uvedené definici aktivity datacihly zadáte tyto typy knihoven: *jar*, *vejce*, *Maven*, *PyPI*, *Cran*.

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

Další podrobnosti najdete v [dokumentaci k datacihlám](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) pro typy knihoven.

## <a name="how-to-upload-a-library-in-databricks"></a>Postup nahrání knihovny v datacihlách

### <a name="you-can-use-the-workspace-ui"></a>Můžete použít uživatelské rozhraní pracovního prostoru:

1. [Použití uživatelského rozhraní pracovního prostoru datacihly](/azure/databricks/libraries/#create-a-library)

2. Chcete-li získat cestu dBFS knihovny přidané pomocí uživatelského rozhraní, můžete použít rozhraní příkazového [řádku datacihly](/azure/databricks/dev-tools/cli/#install-the-cli).

   Knihovny jar jsou obvykle uloženy v dBFS:/úložiště/jar při používání uživatelského rozhraní. Můžete zobrazit seznam všech prostřednictvím rozhraní příkazového řádku: *datacihly FS LS dBFS:/úložiště/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Nebo můžete použít rozhraní příkazového řádku datacihly:

1. Postupujte podle pokynů [ke zkopírování knihovny pomocí](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs) rozhraní příkazového řádku CLI.

2. Použití rozhraní příkazového řádku datacihly [(instalační kroky)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Například pro zkopírování JAR do dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
