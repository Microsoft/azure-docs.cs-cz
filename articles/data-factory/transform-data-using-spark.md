---
title: Transformace dat pomocí aktivity Sparku ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak transformovat data spuštěním Spark programy z kanálu služby Azure data factory pomocí aktivity Sparku.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: douglasl
ms.openlocfilehash: a25505a976be9d9ae38f562591d86ca9b56b8859
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025604"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Sparku ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-spark.md)
> * [Aktuální verze](transform-data-using-spark.md)

Aktivita Spark ve službě Data Factory [kanálu](concepts-pipelines-activities.md) spustí program Sparku na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace. Při použití Spark propojené služby na vyžádání služby Data Factory automaticky vytvoří cluster Spark je just-in-time ke zpracování dat a pak odstraní cluster po dokončení zpracování. 

> [!IMPORTANT]
> Aktivita Spark nepodporuje clustery HDInsight Spark, které pomocí Azure Data Lake Store jako primární úložiště.

## <a name="spark-activity-properties"></a>Vlastnosti aktivity Spark
Tady je ukázková definice JSON aktivity Spark:    

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
| description           | Text popisující, jakým způsobem aktivita naloží.  | Ne       |
| type                  | Pro aktivitu Spark je typ aktivity HDInsightSpark. | Ano      |
| linkedServiceName     | Název propojené služby HDInsight Spark, na kterém se spustí program Sparku. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| SparkJobLinkedService | Azure Storage propojenou službu, která obsahuje Spark soubor úlohy, závislosti a protokoly.  Pokud žádnou hodnotu pro tuto vlastnost nezadáte, použije se úložiště spojené s clusterem HDInsight. Hodnota této vlastnosti lze pouze propojenou službu Azure Storage. | Ne       |
| rootPath              | Kontejner objektů Blob v Azure a složku obsahující soubor Spark. Název souboru je velká a malá písmena. Odkazovat na strukturu složek (další části) v části Podrobnosti o struktuře této složky. | Ano      |
| entryFilePath         | Relativní cesta ke kořenové složce kódu nebo balíčku, Spark. Vstupní soubor musí být soubor Pythonu nebo soubor .jar. | Ano      |
| Název třídy             | Hlavní třída Java/Spark vaší aplikace      | Ne       |
| argumenty             | Seznam argumentů příkazového řádku pro program Sparku. | Ne       |
| proxyUser             | Uživatelský účet zosobnění spuštění programu Sparku | Ne       |
| sparkConfig           | Zadejte hodnoty pro vlastnosti konfigurace Spark vypsané v tomto tématu: [Konfigurace – vlastnosti aplikace Spark](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne       |
| getDebugInfo          | Určuje, kdy soubory protokolu Spark se zkopírují do služby Azure storage používá HDInsight cluster (nebo) je uvedli v sparkJobLinkedService. Povolené hodnoty: NONE, vždy, nebo selhání. Výchozí hodnota: Žádné. | Ne       |

## <a name="folder-structure"></a>struktura složek
Sparkových úloh jsou extensible více než úlohy Pigu a Hivu. Pro úlohy Spark, můžete zadat více závislostí, jako jar balíčky (umístěné v jazyce java cesta k TŘÍDĚ), soubory pythonu (umístěné PYTHONPATH) a další soubory.

Vytvořte následující strukturu složky v úložišti objektů Blob v Azure HDInsight propojená služba odkazuje. Nahrajte závislé soubory odpovídající podsložek v kořenové složce reprezentována **entryFilePath**. Například nahrajte soubory pythonu do pyFiles podsložky a soubory jar do podsložky kromě souborů JAR kořenové složky. V době běhu služba Data Factory očekává, že následující strukturu složek ve službě Azure Blob storage:     

| Cesta                  | Popis                              | Požaduje se | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (uživatel root)            | Kořenová cesta úlohy Sparku na propojenou službu storage | Ano      | Složka |
| &lt;definováno uživatelem &gt; | Cesta odkazující na vstupní soubor úlohy Spark | Ano      | File   |
| . / jars                | Všechny soubory v této složce se nahrála a umístí na cesta třídy java clusteru | Ne       | Složka |
| . / pyFiles             | Všechny soubory v této složce se nahrála a umístí na PYTHONPATH clusteru | Ne       | Složka |
| . / soubory               | Všechny soubory v této složce se nahrála a umístí na prováděcí modul pracovní adresář | Ne       | Složka |
| . / archivů            | Všechny soubory v této složce nekomprimované | Ne       | Složka |
| . / protokoly                | Tato složka obsahuje protokoly z clusteru Spark. | Ne       | Složka |

Tady je příklad pro úložiště, který obsahuje dva soubory úlohy Spark v HDInsight propojená služba odkazuje Azure Blob Storage.

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
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
