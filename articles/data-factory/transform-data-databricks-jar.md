---
title: Transformace dat pomocí JAR pro datacihly
description: Naučte se zpracovávat nebo transformovat data spuštěním jar datacihly v rámci Azure Data Factoryho kanálu.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374010"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformujte data spuštěním aktivity jar v Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Azure Databricks jar v [kanálu Data Factory](concepts-pipelines-activities.md) spouští Sparkovou sklenici v clusteru Azure Databricks. Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definice aktivity JAR pro datacihly

Tady je ukázka definice JSON aktivity JAR pro datacihly:

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

## <a name="databricks-jar-activity-properties"></a>Vlastnosti aktivity JAR pro datacihly

Následující tabulka obsahuje popis vlastností JSON použitých v definici JSON:

|Vlastnost|Popis|Vyžadováno|
|:--|---|:-:|
|name|Název aktivity v kanálu.|Yes|
|description|Text popisující, co aktivita dělá.|No|
|typ|U aktivity JAR pro datacihly je typ aktivity DatabricksSparkJar.|Yes|
|linkedServiceName|Název propojené služby datacihly, na které běží aktivita jar Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) .|Yes|
|mainClassName|Úplný název třídy obsahující metodu Main, která má být provedena. Tato třída musí být obsažena v JAR poskytnutém jako knihovna. Soubor JAR může obsahovat více tříd. Každá třída může obsahovat metodu Main.|Yes|
|parameters|Parametry, které budou předány metodě Main. Tato vlastnost je pole řetězců.|No|
|knihovny|Seznam knihoven, které se mají nainstalovat na cluster, který spustí úlohu. Může to být pole <řetězec, objekt>|Ano (aspoň jeden obsahující metodu mainClassName)|

> [!NOTE]
> **Známý problém** – při použití stejného [interaktivního clusteru](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) pro provozování souběžných aktivit JAR pro datacihly (bez restartování clusteru) je známý problém v datacihlách, kde se v parametrech první aktivity budou používat i následující aktivity. Proto jsou výsledkem předávány nesprávné parametry do následujících úloh. Pokud to chcete zmírnit, použijte místo toho [cluster úloh](compute-linked-services.md#example---using-new-job-cluster-in-databricks) .

## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity datacihly

V předchozí definici aktivity datacihly jste zadali tyto typy knihoven: `jar` , `egg` , `maven` , `pypi` , `cran` .

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

Další informace najdete v dokumentaci k [datacihlám](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) pro typy knihoven.

## <a name="how-to-upload-a-library-in-databricks"></a>Postup nahrání knihovny v datacihlách

### <a name="you-can-use-the-workspace-ui"></a>Můžete použít uživatelské rozhraní pracovního prostoru:

1. [Použití uživatelského rozhraní pracovního prostoru datacihly](/azure/databricks/libraries/#create-a-library)

2. Chcete-li získat cestu dBFS knihovny přidané pomocí uživatelského rozhraní, můžete použít rozhraní příkazového [řádku datacihly](/azure/databricks/dev-tools/cli/#install-the-cli).

   Knihovny jar jsou obvykle uloženy v dBFS:/úložiště/jar při používání uživatelského rozhraní. Můžete zobrazit seznam všech prostřednictvím rozhraní příkazového řádku: *datacihly FS LS dBFS:/úložiště/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Nebo můžete použít rozhraní příkazového řádku datacihly:

1. Postupujte podle pokynů [ke zkopírování knihovny pomocí](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs) rozhraní příkazového řádku CLI.

2. Použití rozhraní příkazového řádku datacihly [(instalační kroky)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Například pro zkopírování JAR do dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak tuto funkci seznámení a ukázka, najdete v [videu](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
