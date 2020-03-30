---
title: 'Transformace dat: Proces & transformace dat '
description: Zjistěte, jak transformovat data nebo zpracovávat data v Azure Data Factory pomocí Hadoop, Machine Learning nebo Azure Data Lake Analytics.
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
ms.openlocfilehash: 5b3e2db9b9769dee7599a2446b272e04cc0bedf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703396"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat v Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Strojové učení](data-factory-azure-ml-batch-execution-activity.md) 
> * [Uložená procedura](data-factory-stored-proc-activity.md)
> * [U-SQL Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Přehled
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma aktivity transformace dat v datové továrně](../transform-data.md).

Tento článek vysvětluje aktivity transformace dat v Azure Data Factory, které můžete použít k transformaci a zpracování nezpracovaná data do předpovědi a přehledy. Transformační aktivita se spouští v výpočetním prostředí, jako je cluster Azure HDInsight nebo Azure Batch. Poskytuje odkazy na články s podrobnými informacemi o každé transformační aktivitě.

Factory podporuje následující aktivity transformace dat, které lze přidat do [kanálů](data-factory-create-pipelines.md) jednotlivě nebo zřetězené s jinou aktivitou.

> [!NOTE]
> Návod s podrobnými pokyny najdete [v tématu Vytvoření kanálu s transformačním](data-factory-build-your-first-pipeline.md) článkem Hive.  
> 
> 

## <a name="hdinsight-hive-activity"></a>Aktivita hdinsight hive
Aktivita HDInsight Hive v kanálu Data Factory provádí dotazy Hive na vašem vlastním nebo na vyžádání windows/linuxového clusteru HDInsight. Podrobnosti o této aktivitě najdete v článku [Aktivita úlu.](data-factory-hive-activity.md) 

## <a name="hdinsight-pig-activity"></a>HDInsight Prasečí aktivita
Aktivita HDInsight Pig v datové továrně provádí dotazy Pig na vašem vlastním nebo na vyžádání založeném hdinsight clusteru windows/linuxu. Podrobnosti o této aktivitě najdete v článku [Aktivita prasat.](data-factory-pig-activity.md) 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce aktivity
Aktivita HDInsight MapReduce v kanálu Data Factory provádí programy MapReduce na vašem vlastním nebo na vyžádání založeného hdinsight clusteru windows/linux. Podrobnosti o této aktivitě najdete v článku [MapReduce Activity.](data-factory-map-reduce.md)

## <a name="hdinsight-streaming-activity"></a>Aktivita streamování HDInsight
Aktivita streamování HDInsight v kanálu Data Factory provádí programy Hadoop Streaming na vašem vlastním nebo na vyžádání windows/linuxového clusteru HDInsight. Podrobnosti o této aktivitě najdete v [tématu HDInsight Streaming activity.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
Aktivita HDInsight Spark v kanálu Data Factory spouští programy Spark ve vašem vlastním clusteru HDInsight. Podrobnosti najdete v tématu [Vyvolání programů Spark z Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Aktivity strojového učení
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikovanou webovou službu Azure Machine Learning pro prediktivní analýzy. Pomocí [aktivity dávkového spuštění](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) v kanálu Azure Data Factory můžete vyvolat webovou službu Machine Learning a vytvořit předpovědi na data v dávce.

V průběhu času je třeba prediktivní modely v experimentech s hodnocením strojového učení přeškolit pomocí nových vstupních datových sad. Po dokončení rekvalifikace, chcete aktualizovat vyhodnocování webové služby s retrained machine learning modelu. Aktualizovat [aktivitu zdrojů](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) můžete použít k aktualizaci webové služby s nově trénovaným modelem.  

Podrobnosti o těchto aktivitách strojového učení najdete v [tématu Použití aktivit strojového učení.](data-factory-azure-ml-batch-execution-activity.md) 

## <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Aktivitu uložené procedury serveru SQL Server v kanálu data factory můžete vyvolat uloženou proceduru v jednom z následujících úložišť dat: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database ve vašem podniku nebo virtuální počítač Azure. Podrobnosti naleznete v článku [Aktivita uložené procedury.](data-factory-stored-proc-activity.md)  

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Aktivita U-SQL služby Data Lake Analytics spouští skript U-SQL v clusteru Azure Data Lake Analytics. Podrobnosti najdete v článku [aktivita U-SQL analýzy dat.](data-factory-usql-activity.md) 

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
Pokud potřebujete transformovat data způsobem, který není podporován data factory, můžete vytvořit vlastní aktivitu s vlastní logiku zpracování dat a použít aktivitu v kanálu. Vlastní aktivitu rozhraní .NET můžete nakonfigurovat tak, aby se spouštěla pomocí služby Azure Batch nebo clusteru Azure HDInsight. Podrobnosti najdete v článku Použití vlastního článku [aktivit.](data-factory-use-custom-activities.md) 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Výpočetní prostředí
Vytvoříte propojenou službu pro výpočetní prostředí a pak použijete propojenou službu při definování transformační aktivity. Existují dva typy výpočetních prostředí podporovaných Data Factory. 

1. **On-Demand**: V tomto případě je výpočetní prostředí plně spravováno společností Data Factory. Je automaticky vytvořen službou Data Factory před odesláním úlohy ke zpracování dat a odebrána po dokončení úlohy. Můžete nakonfigurovat a řídit podrobné nastavení výpočetního prostředí na vyžádání pro provádění úloh, správu clusteru a zaváděcí akce. 
2. **Přineste si vlastní**: V tomto případě můžete zaregistrovat vlastní výpočetní prostředí (například HDInsight cluster) jako propojené služby v datové továrně. Výpočetní prostředí spravujete vy a služba Data Factory ho používá k provádění aktivit. 

Informace o výpočetních službách podporovaných službou Data Factory najdete v článku Výpočetní [propojené služby.](data-factory-compute-linked-services.md) 

## <a name="summary"></a>Souhrn
Azure Data Factory podporuje následující aktivity transformace dat a výpočetní prostředí pro aktivity. Transformační aktivity lze přidat do potrubí jednotlivě nebo zřetězené s jinou aktivitou.

| Aktivita transformace dat | Výpočetní prostředí |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streamování Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |Virtuální počítač Azure |
| [Uložená procedura](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse nebo SQL Server |
| [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [Dotnet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] nebo Azure Batch |

