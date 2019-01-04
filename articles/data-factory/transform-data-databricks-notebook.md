---
title: Transformace dat pomocí poznámkového bloku Databricks – Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpracovat nebo transformovat data pomocí poznámkového bloku Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 8ab6dad36bf47430a925d21ca2464286e7e70002
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022066"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformovat data spuštěním poznámkového bloku Databricks

Aktivity poznámkového bloku Azure Databricks ve [kanálu služby Data Factory](concepts-pipelines-activities.md) spuštění poznámkového bloku Databricks v pracovním prostoru Azure Databricks. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definice aktivity poznámkového bloku Databricks

Tady je ukázková definice JSON aktivity poznámkového bloku Databricks:

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

Následující tabulka popisuje vlastnostech JSON použitých v definici JSON:

|Vlastnost|Popis|Požaduje se|
|---|---|---|
|jméno|Název aktivity v kanálu.|Ano|
|description|Text popisující, jakým způsobem aktivita naloží.|Ne|
|type|Pro aktivitu poznámkového bloku Databricks je typ aktivity DatabricksNotebook.|Ano|
|linkedServiceName|Název propojené služby Databricks, na kterém běží poznámkového bloku Databricks. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku.|Ano|
|notebookPath|Absolutní cesta ke spuštění v pracovním prostoru Databricks Poznámkový blok. Tato cesta musí začínat lomítkem.|Ano|
|baseParameters|Pole párů klíč-hodnota. Základní parametry můžete použít pro každé spuštění aktivity. Pokud poznámkový blok má parametr, který není zadán, použije se výchozí hodnota z poznámkového bloku. Další informace o parametrech v [poznámkových bloků Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Ne|
|Knihovny|Seznam knihovny, které chcete nainstalovat na clusteru, který provede úlohu. Pole může být \<řetězec, objekt >.|Ne|


## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity Databricks

V definici aktivity výše Databricks zadáte tyto knihovny typů: *jar*, *egg*, *maven*, *pypi*,  *cran*.

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

Další podrobnosti najdete v tématu [dokumentace k Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) knihovny typů.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak nahrát knihovnu v Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Používání pracovního prostoru Databricks uživatelského rozhraní](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat cestu dbfs knihovny přidat pomocí uživatelského rozhraní, můžete použít [rozhraní příkazového řádku Databricks (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Obvykle jsou uložené na soubor Jar knihovny pod dbfs: / funkce FileStore/jars při používání uživatelského rozhraní. Můžete zobrazit seznam všech přes rozhraní příkazového řádku: *databricks fs ls dbfs: / funkce FileStore/JAR*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Knihovna kopírování pomocí rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Příklad: *databricks fs cp SparkPi sestavení 0.1.jar dbfs: / funkce FileStore/JAR*
