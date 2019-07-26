---
title: Transformace dat pomocí poznámkového bloku datacihly – Azure | Microsoft Docs
description: Naučte se zpracovávat nebo transformovat data spuštěním poznámkového bloku datacihly.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 2bc8b84d4b98036acc93788dee88444786df139e
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335853"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformujte data spuštěním poznámkového bloku datacihly.

Aktivita poznámkového bloku Azure Databricks v [kanálu Data Factory](concepts-pipelines-activities.md) spustí v pracovním prostoru Azure Databricks Poznámkový blok datacihly. Tento článek se sestavuje na článku  [aktivity transformace dat](transform-data.md), který představuje obecný přehled transformace dat a podporovaných transformačních aktivit. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definice aktivity poznámkového bloku datacihly

Tady je ukázka definice JSON aktivity poznámkového bloku datacihly:

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

## <a name="databricks-notebook-activity-properties"></a>Vlastnosti aktivity poznámkového bloku datacihly

Následující tabulka obsahuje popis vlastností JSON použitých v definici JSON:

|Vlastnost|Popis|Požadováno|
|---|---|---|
|name|Název aktivity v kanálu.|Ano|
|description|Text popisující, co aktivita dělá.|Ne|
|type|V případě aktivity poznámkového bloku datacihly je typ aktivity DatabricksNotebook.|Ano|
|linkedServiceName|Název propojené služby datacihly, na které se Poznámkový blok datacihly spouští. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám](compute-linked-services.md) Compute.|Ano|
|notebookPath|Absolutní cesta poznámkového bloku, který má být spuštěn v pracovním prostoru datacihly. Tato cesta musí začínat lomítkem.|Ano|
|baseParameters|Pole párů klíč-hodnota. Základní parametry lze použít pro každý běh aktivity. Pokud Poznámkový blok převezme parametr, který není zadaný, použije se výchozí hodnota z poznámkového bloku. Přečtěte si další informace o parametrech v poznámkových [blocích](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)datacihly.|Ne|
|Knihovna|Seznam knihoven, které se mají nainstalovat na cluster, který spustí úlohu. Může to být pole typu \<String a Object >.|Ne|


## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity datacihly

Ve výše uvedené definici aktivity datacihly zadáte tyto typy knihoven: *jar*, *vaječný*, *WHL*, *Maven*, *PyPI*, *Cran*.

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

Další podrobnosti najdete v [dokumentaci](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) k datacihlám pro typy knihoven.

## <a name="how-to-upload-a-library-in-databricks"></a>Postup nahrání knihovny v datacihlách

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Použití uživatelského rozhraní pracovního prostoru datacihly](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat cestu dBFS knihovny přidané pomocí uživatelského rozhraní, můžete použít rozhraní příkazového [řádku datacihly (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Knihovny jar jsou obvykle uloženy v dBFS:/úložiště/jar při používání uživatelského rozhraní. Seznamte se všemi prostřednictvím rozhraní příkazového řádku: datacihly *FS LS dBFS:/úložiště/jar*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopírování knihovny pomocí rozhraní příkazového řádku datacihly](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Příklad: *datacihly FS CP sparkpi-Assembly-0,1. jar dBFS:/úložiště/jar*
