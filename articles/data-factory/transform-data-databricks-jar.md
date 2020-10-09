---
title: Transformace dat pomocí JAR pro datacihly
description: Naučte se zpracovávat nebo transformovat data spuštěním JAR pro datacihly.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81414616"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformujte data spuštěním aktivity jar v Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Azure Databricks jar v [kanálu Data Factory](concepts-pipelines-activities.md) spouští Sparkovou sklenici v clusteru Azure Databricks. Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md)   , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

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

|Vlastnost|Popis|Povinné|
|:--|---|:-:|
|name|Název aktivity v kanálu.|Ano|
|Popis|Text popisující, co aktivita dělá.|No|
|typ|U aktivity JAR pro datacihly je typ aktivity DatabricksSparkJar.|Ano|
|linkedServiceName|Název propojené služby datacihly, na které běží aktivita jar Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md)   .|Ano|
|mainClassName|Úplný název třídy obsahující metodu Main, která má být provedena. Tato třída musí být obsažena v JAR poskytnutém jako knihovna.|Ano|
|parameters|Parametry, které budou předány metodě Main.  Toto je pole řetězců.|No|
|knihovny|Seznam knihoven, které se mají nainstalovat na cluster, který spustí úlohu. Může to být pole <řetězec, objekt>|Ano (aspoň jeden obsahující metodu mainClassName)|

> [!NOTE]
> **Známý problém** – při použití stejného [interaktivního clusteru](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) pro provozování souběžných aktivit JAR pro datacihly (bez restartování clusteru) je známý problém v datacihlách, kde se v parametrech první aktivity budou používat i následující aktivity. Proto jsou výsledkem předávány nesprávné parametry do následujících úloh. Pokud to chcete zmírnit, použijte místo toho [cluster úloh](compute-linked-services.md#example---using-new-job-cluster-in-databricks) . 

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

Další podrobnosti najdete v [dokumentaci k datacihlám](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) pro typy knihoven.

## <a name="how-to-upload-a-library-in-databricks"></a>Postup nahrání knihovny v datacihlách

#### <a name="using-databricks-workspace-ui"></a>[Použití uživatelského rozhraní pracovního prostoru datacihly](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat cestu dBFS knihovny přidané pomocí uživatelského rozhraní, můžete použít rozhraní příkazového [řádku datacihly (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Knihovny jar jsou obvykle uloženy v dBFS:/úložiště/jar při používání uživatelského rozhraní. Můžete zobrazit seznam všech prostřednictvím rozhraní příkazového řádku: *datacihly FS LS dBFS:/úložiště/Job-Jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopírování knihovny pomocí rozhraní příkazového řádku datacihly](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Použijte rozhraní příkazového řádku datacihly [(instalační kroky)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Příklad – kopírování JAR do dBFS: *dBFS CP sparkpi-Assembly-0,1. jar dBFS:/docs/SparkPi. jar*
