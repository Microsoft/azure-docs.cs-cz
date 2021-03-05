---
title: Transformace dat pomocí poznámkového bloku datacihly
description: Naučte se zpracovávat nebo transformovat data spuštěním poznámkového bloku datacihly v Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: fea572c2e75f62b5e7e7b4634e37da348bdcdaf1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183484"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformujte data spuštěním poznámkového bloku datacihly.
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita poznámkového bloku Azure Databricks v [kanálu Data Factory](concepts-pipelines-activities.md) spustí v pracovním prostoru Azure Databricks Poznámkový blok datacihly. Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit. Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

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

|Vlastnost|Popis|Vyžadováno|
|---|---|---|
|name|Název aktivity v kanálu.|Ano|
|description|Text popisující, co aktivita dělá.|Ne|
|typ|V případě aktivity poznámkového bloku datacihly je typ aktivity DatabricksNotebook.|Ano|
|linkedServiceName|Název propojené služby datacihly, na které se Poznámkový blok datacihly spouští. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) .|Ano|
|notebookPath|Absolutní cesta poznámkového bloku, který má být spuštěn v pracovním prostoru datacihly. Tato cesta musí začínat lomítkem.|Ano|
|baseParameters|Pole párů Key-Value. Základní parametry lze použít pro každý běh aktivity. Pokud Poznámkový blok převezme parametr, který není zadaný, použije se výchozí hodnota z poznámkového bloku. Přečtěte si další informace o parametrech v [poznámkových blocích datacihly](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Ne|
|knihovny|Seznam knihoven, které se mají nainstalovat na cluster, který spustí úlohu. Může to být pole \<string, object> .|Ne|

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

Další podrobnosti najdete v dokumentaci k [datacihlám](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) pro typy knihoven.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Předávání parametrů mezi poznámkovými bloky a Data Factory

Parametry datové továrny můžete předat do poznámkových bloků pomocí vlastnosti *baseParameters* v aktivitě datacihly.

V některých případech je možné, že budete potřebovat předávat určité hodnoty z poznámkového bloku zpátky do služby Data Factory, které se dají použít k řízení toku (podmíněných kontrol) v datové továrně nebo jejich využívání v případě podřízených aktivit (omezení velikosti je 2 MB).

1. Ve vašem poznámkovém bloku můžete zavolat [dbutils. Poznámkový blok ("ReturnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) a odpovídající znak "ReturnValue" bude vrácen do objektu pro vytváření dat.

2. Výstup ve službě Data Factory můžete využívat pomocí výrazu, jako je například `@{activity('databricks notebook activity name').output.runOutput}` . 

   > [!IMPORTANT]
   > Pokud předáváte objekt JSON, můžete načíst hodnoty připojením názvů vlastností. Příklad: `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Postup nahrání knihovny v datacihlách

### <a name="you-can-use-the-workspace-ui"></a>Můžete použít uživatelské rozhraní pracovního prostoru:

1. [Použití uživatelského rozhraní pracovního prostoru datacihly](/azure/databricks/libraries/#create-a-library)

2. Chcete-li získat cestu dBFS knihovny přidané pomocí uživatelského rozhraní, můžete použít rozhraní příkazového [řádku datacihly](/azure/databricks/dev-tools/cli/#install-the-cli).

   Knihovny jar jsou obvykle uloženy v dBFS:/úložiště/jar při používání uživatelského rozhraní. Můžete zobrazit seznam všech prostřednictvím rozhraní příkazového řádku: *datacihly FS LS dBFS:/úložiště/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Nebo můžete použít rozhraní příkazového řádku datacihly:

1. Postupujte podle pokynů [ke zkopírování knihovny pomocí](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs) rozhraní příkazového řádku CLI.

2. Použití rozhraní příkazového řádku datacihly [(instalační kroky)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Například pro zkopírování JAR do dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
