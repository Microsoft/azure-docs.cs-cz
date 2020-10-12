---
title: 'Transformace dat: proces & transformuje data '
description: Naučte se transformovat data nebo zpracovávat data v Azure Data Factory pomocí Hadoop, Machine Learning nebo Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: eb320cb71de43c40522bf93213fd98247a0d5b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436293"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Transformace dat ve verzi Azure Data Factory 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Uložená procedura](data-factory-stored-proc-activity.md)
> * [U-SQL Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní rozhraní .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Přehled
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktivity transformace dat v Data Factory](../transform-data.md).

Tento článek vysvětluje aktivity transformace dat v Azure Data Factory, které můžete použít k transformaci a zpracování nezpracovaných dat do předpovědi a přehledů. Transformační aktivita se spouští v výpočetním prostředí, jako je cluster Azure HDInsight nebo Azure Batch. Obsahuje odkazy na články s podrobnými informacemi o každé aktivitě transformace.

Data Factory podporuje následující aktivity transformace dat, které je možné přidat do [kanálů](data-factory-create-pipelines.md) jednotlivě nebo zřetězit s jinou aktivitou.

> [!NOTE]
> Návod s podrobnými pokyny najdete v článku [vytvoření kanálu pomocí transformace podregistru](data-factory-build-your-first-pipeline.md) .  
> 
> 

## <a name="hdinsight-hive-activity"></a>Aktivita v podregistru HDInsight
Aktivita podregistru HDInsight v Data Factoryovém kanálu spouští dotazy na podregistry ve vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání. Podrobnosti o této aktivitě najdete v článku o [aktivitě podregistru](data-factory-hive-activity.md) . 

## <a name="hdinsight-pig-activity"></a>Aktivita prasete v HDInsight
Aktivita prasete v HDInsight v kanálu Data Factory spouští dotazy na prasaty na vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání. Podrobnosti o této aktivitě najdete v článku o [aktivitě prasete](data-factory-pig-activity.md) . 

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce HDInsight
Aktivita MapReduce služby HDInsight v kanálu Data Factory spouští programy MapReduce na vašem clusteru HDInsight založeném na systému Windows nebo Linux na vyžádání. Podrobnosti o této aktivitě najdete v článku o [aktivitě MapReduce](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Aktivita streamování HDInsight
Aktivita streamování HDInsight v kanálu Data Factory spouští programy pro streamování Hadoop na vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání. Podrobnosti o této aktivitě najdete v tématu [aktivita streamování HDInsight](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
Aktivita HDInsight Spark v kanálu Data Factory spouští programy Spark ve vlastním clusteru HDInsight. Podrobnosti najdete v tématu [vyvolání programů Spark z Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning aktivity
Azure Data Factory vám umožní snadno vytvářet kanály, které používají publikovanou webovou službu Azure Machine Learning pro prediktivní analýzy. Pomocí [aktivity dávkového spuštění](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) v kanálu Azure Data Factory můžete vyvolat webovou službu Machine Learning a předpovědi data ve službě Batch.

Prediktivní modely v Machine Learning experimenty bodování musí být v průběhu času převlakované pomocí nových vstupních datových sad. Až budete s rekurzem hotovi, chcete aktualizovat webovou službu bodování pomocí Machine Learningho modelu. [Aktivitu aktualizovat prostředek](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) můžete použít k aktualizaci webové služby pomocí nově vyučeného modelu.  

Podrobnosti o těchto Machine Learningch aktivitách najdete v tématu [použití Machine Learning aktivit](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Pomocí aktivity uložená procedura SQL Server v kanálu Data Factory můžete vyvolat uloženou proceduru v jednom z následujících úložišť dat: Azure SQL Database, Azure synapse Analytics (dříve SQL Data Warehouse), SQL Server databáze ve vašem podniku nebo na virtuálním počítači Azure. Podrobnosti najdete v článku [aktivita uložených procedur](data-factory-stored-proc-activity.md) .  

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita Data Lake Analytics U-SQL
Aktivita Data Lake Analytics U-SQL spouští skript U-SQL v clusteru Azure Data Lake Analytics. Podrobnosti najdete v článku o [aktivitě data Analytics U-SQL](data-factory-usql-activity.md) . 

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
Pokud potřebujete transformovat data způsobem, který není podporován Data Factory, můžete vytvořit vlastní aktivitu s vlastní logikou zpracování dat a použít aktivitu v kanálu. Vlastní aktivitu rozhraní .NET můžete nakonfigurovat tak, aby běžela buď pomocí služby Azure Batch, nebo clusteru Azure HDInsight. Podrobnosti najdete v článku [použití vlastních aktivit](data-factory-use-custom-activities.md) . 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Výpočetní prostředí
Vytvoříte propojenou službu pro výpočetní prostředí a pak při definování aktivity transformace použijete propojenou službu. Data Factory podporují dva typy výpočetních prostředí. 

1. **Na vyžádání**: v tomto případě je výpočetní prostředí plně spravované pomocí Data Factory. Služba Data Factory ji automaticky vytvoří před odesláním úlohy ke zpracování dat a po jejím dokončení se odstraní. Můžete nakonfigurovat a řídit podrobné nastavení výpočetního prostředí na vyžádání pro provádění úloh, správu clusterů a spouštěcí akce. 
2. **Přineste si vlastní**: v takovém případě můžete zaregistrovat vlastní výpočetní prostředí (například cluster HDInsight) jako propojenou službu v Data Factory. Výpočetní prostředí je spravováno vámi a služba Data Factory ji používá ke spuštění aktivit. 

Seznamte se s výpočetními službami podporovanými nástrojem Data Factory v článku věnovaném [výpočetním propojeným službám](data-factory-compute-linked-services.md) . 

## <a name="summary"></a>Shrnutí
Azure Data Factory podporuje následující aktivity transformace dat a výpočetní prostředí pro aktivity. Transformační aktivity lze přidat do kanálů buď individuálně, nebo pomocí zřetězení s jinou aktivitou.

| Aktivita transformace dat | Výpočetní prostředí |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streamování Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |Virtuální počítač Azure |
| [Uložená procedura](data-factory-stored-proc-activity.md) |Azure SQL, Azure synapse Analytics nebo SQL Server |
| [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] nebo Azure Batch |

