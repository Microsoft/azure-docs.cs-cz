---
title: Transformace dat pomocí aktivity Sparku
description: Naučte se, jak transformovat data spuštěním programů Spark z Azure Data Factory kanálu pomocí aktivity Sparku.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: cac64b17e7aad9aa2bf88386f21d5f82b3013fa3
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566771"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformuje data pomocí aktivity Sparku v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-spark.md)
> * [Aktuální verze](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita Spark v [kanálu](concepts-pipelines-activities.md) Data Factory spustí program Spark na [vašem](compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  . Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit. Když použijete propojenou službu Spark na vyžádání, Data Factory automaticky vytvoří cluster Spark za vás, abyste mohli data zpracovat a pak cluster po dokončení zpracování odstraní. 


## <a name="spark-activity-properties"></a>Vlastnosti aktivity Spark
Tady je ukázka definice JSON aktivity Spark:    

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

Následující tabulka obsahuje popis vlastností JSON použitých v definici JSON:

| Vlastnost              | Popis                              | Povinné |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Název aktivity v kanálu.    | Yes      |
| description           | Text popisující, co aktivita dělá.  | Ne       |
| typ                  | Pro aktivitu Spark je typ aktivity HDInsightSpark. | Yes      |
| linkedServiceName     | Název propojené služby HDInsight Spark, na které běží program Spark Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Yes      |
| SparkJobLinkedService | Propojená služba Azure Storage, která obsahuje soubor úlohy Spark, závislosti a protokoly. Tady se podporují jenom propojené služby **[Azure Blob Storage](./connector-azure-blob-storage.md)** a **[adls Gen2](./connector-azure-data-lake-storage.md)** . Pokud pro tuto vlastnost nezadáte hodnotu, použije se úložiště přidružené ke clusteru HDInsight. Hodnota této vlastnosti může být pouze propojená služba Azure Storage. | Ne       |
| rootPath              | Kontejner a složka Azure Blob, které obsahují soubor Spark. V názvu souboru se rozlišují malá a velká písmena. Podrobnosti o struktuře této složky najdete v části struktura složky (další oddíl). | Yes      |
| entryFilePath         | Relativní cesta ke kořenové složce kódu nebo balíčku Spark Vstupní soubor musí být buď soubor Pythonu, nebo soubor. jar. | Yes      |
| NázevTřídy             | Hlavní třída Java/Spark aplikace      | Ne       |
| náhodné             | Seznam argumentů příkazového řádku pro program Spark. | Ne       |
| proxyUser             | Uživatelský účet, který se má zosobnit pro spuštění programu Spark | Ne       |
| sparkConfig           | Zadejte hodnoty pro vlastnosti konfigurace Sparku uvedené v tématu: [Konfigurace Spark – vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne       |
| GetDebugInfo –          | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště Azure používaného clusterem HDInsight (nebo), který určuje sparkJobLinkedService. Povolené hodnoty: žádné, vždy nebo chyba. Výchozí hodnota: Žádný. | Ne       |

## <a name="folder-structure"></a>Struktura složek
Úlohy Spark jsou více rozšiřitelnější než při úlohách vepřového a podregistru. Pro úlohy Spark můžete zadat několik závislostí, jako jsou například balíčky jar (umístěné v cestě třídy Java), soubory Pythonu (umístěné na PYTHONPATH) a všechny další soubory.

Vytvořte ve službě Azure Blob Storage, na kterou odkazuje propojená služba HDInsight, následující strukturu složek. Pak nahrajte závislé soubory do příslušných podadresářů v kořenové složce reprezentované **entryFilePath**. Například nahrajte soubory Pythonu do podsložky pyFiles a soubory jar do podsložky jar kořenové složky. V době běhu Služba Data Factory očekává v úložišti objektů BLOB v Azure následující strukturu složek:     

| Cesta                  | Popis                              | Požaduje se | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` zobrazuje            | Kořenová cesta úlohy Spark v propojené službě úložiště | Ano      | Složka |
| &lt;definováno uživatelem &gt; | Cesta ukazující na vstupní soubor úlohy Spark | Yes      | Soubor   |
| ./jars                | Všechny soubory v této složce se nahrají a umístí do cesty tříd Java clusteru. | Ne       | Složka |
| ./pyFiles             | Všechny soubory v této složce se nahrají a umístí do PYTHONPATH clusteru. | Ne       | Složka |
| ./files               | Všechny soubory v této složce se nahrají a umístí do pracovního adresáře prováděcího modulu. | Ne       | Složka |
| ./archives            | Všechny soubory v této složce jsou nekomprimované. | Ne       | Složka |
| ./logs                | Složka, která obsahuje protokoly z clusteru Spark. | Ne       | Složka |

Tady je příklad úložiště, které obsahuje dva soubory úloh Spark v Azure Blob Storage, na které odkazuje propojená služba HDInsight.

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
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
