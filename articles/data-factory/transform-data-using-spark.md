---
title: "Transformace dat pomocí aktivity Spark v Azure Data Factory | Microsoft Docs"
description: "Zjistěte, jak k transformaci dat tak, že spustíte Spark programy kanál služby Azure data factory pomocí Spark aktivity."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: e4cc412f9c6e37d6e6c3b030a934d572a7319011
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Spark v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-spark.md)
> * [Verze 2 – Preview](transform-data-using-spark.md)

Aktivita Spark v datové továrně [kanálu](concepts-pipelines-activities.md) spustí Spark program na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivit transformace dat](transform-data.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace. Při použití služby na vyžádání Spark propojené služby Data Factory automaticky vytvoří cluster Spark pro můžete v běhu při zpracování dat a poté odstraní clusteru po dokončení zpracování. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivity Spark v V1](v1/data-factory-spark.md).

> [!IMPORTANT]
> Aktivita Spark nepodporuje clustery HDInsight Spark, které používají Azure Data Lake Store jako primární úložiště.

## <a name="spark-activity-properties"></a>Vlastnosti aktivity Spark
Zde je ukázka definici JSON Spark aktivity:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

Následující tabulka popisuje vlastnostech JSON použitých v definici JSON:

| Vlastnost              | Popis                              | Požaduje se |
| --------------------- | ---------------------------------------- | -------- |
| jméno                  | Název aktivity v kanálu.    | Ano      |
| description           | Popisuje, jakým způsobem aktivita naloží text.  | Ne       |
| type                  | Typ aktivity pro aktivitu Spark je HDInsightSpark. | Ano      |
| linkedServiceName     | Název propojené služby HDInsight Spark, který je spuštěn Spark program. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku. | Ano      |
| SparkJobLinkedService | Azure Storage propojená služba, která obsahuje Spark soubor úlohy, závislosti a protokoly.  Pokud hodnotu pro tuto vlastnost nezadáte, použije se úložiště přidružený k clusteru HDInsight. Hodnota této vlastnosti lze pouze propojenou službu úložiště Azure. | Ne       |
| rootPath              | Kontejner objektů Blob v Azure a složky, která obsahuje soubor Spark. Název souboru je malá a velká písmena. Struktura složek najdete části (další části) Další informace o struktuře této složky. | Ano      |
| entryFilePath         | Relativní cesta ke kořenové složce Spark kódu nebo balíčku. | Ano      |
| className             | Hlavní třídy aplikace Java/Spark      | Ne       |
| Argumenty             | Seznam argumentů příkazového řádku pro Spark program. | Ne       |
| proxyUser             | Uživatelský účet zosobnění spuštění programu Spark | Ne       |
| sparkConfig           | Zadejte hodnoty pro vlastnosti konfigurace Spark vypsané v tomto tématu: [Spark konfigurace – vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne       |
| getDebugInfo          | Určuje, kdy soubory protokolu Spark se zkopírují do úložiště Azure používá HDInsight cluster (nebo) zadaný ve sparkJobLinkedService. Povolené hodnoty: None, vždy nebo selhání. Výchozí hodnota: žádné. | Ne       |

## <a name="folder-structure"></a>Struktura složek
Spark úlohy jsou extensible více než úlohy Pig nebo Hive. Pro úlohy Spark, můžete zadat více závislostí, jako jar balíčky (umístěné v jazyce java cesty pro třídy), soubory python (umístit na PYTHONPATH) a všechny další soubory.

Vytvořte následující strukturu složek ve službě Azure Blob storage, na kterou odkazuje propojená služba HDInsight. Potom obsah nahrajete soubory závislé na příslušné podsložek v kořenové složce reprezentována **entryFilePath**. Například nahrajte python soubory do pyFiles podsložky a soubory jar do podsložky JAR kořenové složky. Služba Data Factory v době běhu očekává následující strukturu složek ve službě Azure Blob storage:     

| Cesta                  | Popis                              | Požaduje se | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (uživatel root)            | Kořenová cesta úlohy Spark v úložišti propojená služba | Ano      | Složka |
| &lt;Uživatelem definované &gt; | Cesta k souboru položka úlohy Spark | Ano      | File   |
| ./jars                | Všechny soubory v této složce se nahrála a umístit na cestě třídy java clusteru | Ne       | Složka |
| . / pyFiles             | Všechny soubory v této složce se nahrála a umístit do PYTHONPATH clusteru | Ne       | Složka |
| . / soubory               | Všechny soubory v této složce se nahrála a umístit na vykonavatele pracovní adresář | Ne       | Složka |
| . / archivy            | Všechny soubory v této složce nekomprimované | Ne       | Složka |
| . / protokoly                | Složky, která obsahuje protokoly z clusteru Spark. | Ne       | Složka |

Tady je příklad pro úložiště, který obsahuje dva soubory úlohy Spark v Azure Blob Storage odkazuje propojená služba HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning dávkového spuštění aktivity](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
