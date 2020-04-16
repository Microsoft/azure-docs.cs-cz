---
title: Transformace dat pomocí aktivity Spark
description: Zjistěte, jak transformovat data spuštěním programů Spark z kanálu datové továrny Azure pomocí aktivity sparku.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: c39575e8ea60a091124c633f8958ec36e8a61885
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418843"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Spark v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-spark.md)
> * [Aktuální verze](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Spark v [kanálu](concepts-pipelines-activities.md) Data Factory spustí program Spark na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) cluster nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight. Tento článek vychází z článku [aktivit transformace dat,](transform-data.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit. Když používáte propojenou službu Na vyžádání, Data Factory automaticky vytvoří cluster Spark pro zpracování dat a po dokončení zpracování cluster uhasí. 


## <a name="spark-activity-properties"></a>Vlastnosti aktivity jiskry
Zde je ukázka json definice aktivity Spark:    

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
        "rootPath": "adfspark",
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

Následující tabulka popisuje vlastnosti JSON použité v definici JSON:

| Vlastnost              | Popis                              | Požaduje se |
| --------------------- | ---------------------------------------- | -------- |
| jméno                  | Název aktivity v kanálu.    | Ano      |
| description           | Text popisující, co aktivita dělá.  | Ne       |
| type                  | Pro aktivitu Spark je typ aktivity HDInsightSpark. | Ano      |
| linkedServiceName     | Název služby HDInsight Spark Linked Service, na které běží program Spark. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| Služba SparkJobLinkedService | Propojená služba Azure Storage, která obsahuje soubor úloh, závislostí a protokolů Spark.  Pokud nezadáte hodnotu pro tuto vlastnost, použije se úložiště přidružené ke clusteru HDInsight. Hodnota této vlastnosti může být jenom propojené služby Azure Storage. | Ne       |
| rootPath              | Kontejner a složka objektu Blob Azure, která obsahuje soubor Spark. Název souboru rozlišuje malá a velká písmena. Podrobnosti o struktuře této složky naleznete v části Struktura složek (další část). | Ano      |
| entryFilePath         | Relativní cesta ke kořenové složce kódu Spark/balíčku. Soubor položky musí být soubor Pythonu nebo soubor .jar. | Ano      |
| Classname             | Hlavní třída Java/Spark aplikace      | Ne       |
| Argumenty             | Seznam argumentů příkazového řádku do programu Spark. | Ne       |
| proxyUživatel             | Uživatelský účet, který se má zosobnit za účelem spuštění programu Spark | Ne       |
| sparkConfig           | Zadejte hodnoty pro vlastnosti konfigurace Spark uvedené v tématu: [Konfigurace jiskry - vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne       |
| getDebugInfo          | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště Azure používaného clusterem HDInsight (nebo) určeného službou sparkJobLinkedService. Povolené hodnoty: Žádné, Vždy nebo Selhání. Výchozí hodnota: Žádný. | Ne       |

## <a name="folder-structure"></a>Struktura složek
Spark pracovních míst jsou rozšiřitelnější než Pig / Hive pracovních míst. Pro úlohy Spark můžete poskytnout více závislostí, jako jsou balíčky jar (umístěné v java CLASSPATH), soubory pythonu (umístěné na PYTHONPATH) a všechny ostatní soubory.

Vytvořte následující strukturu složek v úložišti objektů Blob Azure, na kterou odkazuje propojená služba HDInsight. Potom nahrajte závislé soubory do příslušných podsložek v kořenové složce reprezentované **entryFilePath**. Například nahrajte soubory pythonu do podsložky pyFiles a jar do podsložky sklenic kořenové složky. Za běhu služba Data Factory očekává následující strukturu složek v úložišti objektů blob Azure:     

| Cesta                  | Popis                              | Požaduje se | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(kořen)            | Kořenová cesta úlohy Spark ve službě propojené úložiště | Ano      | Složka |
| &lt;definováno uživatelem&gt; | Cesta směřující k vstupnímu souboru úlohy Spark | Ano      | File   |
| ./sklenice                | Všechny soubory v této složce jsou nahrány a umístěny na cestu třídy java clusteru | Ne       | Složka |
| ./pyFiles             | Všechny soubory v této složce jsou nahrány a umístěny na PYTHONPATH clusteru | Ne       | Složka |
| ./soubory               | Všechny soubory v této složce jsou nahrány a umístěny do pracovního adresáře vykonavatele | Ne       | Složka |
| ./archivy            | Všechny soubory v této složce jsou nekomprimované | Ne       | Složka |
| ./protokoly                | Složka, která obsahuje protokoly z clusteru Spark. | Ne       | Složka |

Tady je příklad úložiště obsahující dva soubory úloh Spark v úložišti objektů blob Azure, na které odkazuje propojená služba HDInsight.

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
## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
