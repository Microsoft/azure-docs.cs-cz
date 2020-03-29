---
title: Transformace dat
description: Zjistěte, jak transformovat data nebo zpracovávat data v Azure Data Factory pomocí Hadoop, Machine Learning nebo Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 1f920f2672c19455a8e8ac979e8d6d1eb14d4c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924301"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat v Azure Data Factory

> [!div class="op_single_selector"]
> * [Mapování toku dat](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Streamování HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Strojové učení](transform-data-using-machine-learning.md) 
> * [Uložená procedura](transform-data-using-stored-procedure.md)
> * [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Poznámkový blok Databricks](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [Vlastní .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Přehled
Tento článek vysvětluje aktivity transformace dat v Azure Data Factory, které můžete použít k transformaci a zpracování nezpracovaná data do předpovědi a přehledy ve velkém měřítku. Transformační aktivita se spouští v výpočetním prostředí, jako jsou Azure Databricks nebo Azure HDInsight. Poskytuje odkazy na články s podrobnými informacemi o každé transformační aktivitě.

Factory podporuje následující aktivity transformace dat, které lze přidat do [kanálů](concepts-pipelines-activities.md) jednotlivě nebo zřetězené s jinou aktivitou.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Nativně se transformace ve Službě Azure Data Factory pomocí toků dat

### <a name="mapping-data-flows"></a>Toky dat mapování

Mapování toků dat jsou vizuálně navržené transformace dat v Azure Data Factory. Toky dat umožňují technikům dat vyvíjet logiku grafické transformace dat bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci kanálů Azure Data Factory, které používají clustery Spark s horizontálním navýšením kapacity. Aktivity toku dat lze zprovoznit prostřednictvím stávajících možností plánování, řízení, toku a monitorování v datové toku. Další informace naleznete v tématu [mapování toků dat](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Hašteření datových toků

Hašteření datových toků v Azure Data Factory vám umožní provést přípravu dat bez kódu v cloudovém měřítku iterativně. Hašteření datových toků se integruje s [Power Query Online](https://docs.microsoft.com/power-query/) a zpřístupňuje funkce Power Query M pro datové tahání v cloudovém měřítku prostřednictvím spuštění jiskry. Další informace naleznete v [tématu hádání datových toků](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Externí transformace

Volitelně můžete ručně kódovat transformace a spravovat externí výpočetní prostředí sami.

### <a name="hdinsight-hive-activity"></a>Aktivita hdinsight hive
Aktivita HDInsight Hive v kanálu Data Factory provádí dotazy Hive na vašem vlastním nebo na vyžádání windows/linuxového clusteru HDInsight. Podrobnosti o této aktivitě najdete v článku [aktivity Hive.](transform-data-using-hadoop-hive.md) 

### <a name="hdinsight-pig-activity"></a>HDInsight Prasečí aktivita
Aktivita HDInsight Pig v datové továrně provádí dotazy Pig na vašem vlastním nebo na vyžádání založeném hdinsight clusteru windows/linuxu. Podrobnosti o této aktivitě najdete v článku [aktivity prasete.](transform-data-using-hadoop-pig.md) 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce aktivity
Aktivita HDInsight MapReduce v kanálu Data Factory provádí programy MapReduce na vašem vlastním nebo na vyžádání založeného hdinsight clusteru windows/linux. Podrobnosti o této aktivitě najdete v článku [MapReduce aktivity.](transform-data-using-hadoop-map-reduce.md)

### <a name="hdinsight-streaming-activity"></a>Aktivita streamování HDInsight
Aktivita streamování HDInsight v kanálu Data Factory provádí programy Hadoop Streaming na vašem vlastním nebo na vyžádání windows/linuxového clusteru HDInsight. Podrobnosti o této aktivitě najdete v [tématu HDInsight Streaming activity.](transform-data-using-hadoop-streaming.md)

### <a name="hdinsight-spark-activity"></a>Aktivita HDInsight Spark
Aktivita HDInsight Spark v kanálu Data Factory spouští programy Spark ve vašem vlastním clusteru HDInsight. Podrobnosti najdete v tématu [Vyvolání programů Spark z Azure Data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Aktivity strojového učení
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikovanou webovou službu Azure Machine Learning pro prediktivní analýzy. Pomocí [aktivity dávkového spuštění](transform-data-using-machine-learning.md) v kanálu Azure Data Factory můžete vyvolat webovou službu Machine Learning a vytvořit předpovědi na data v dávce.

V průběhu času je třeba prediktivní modely v experimentech s hodnocením strojového učení přeškolit pomocí nových vstupních datových sad. Po dokončení rekvalifikace, chcete aktualizovat vyhodnocování webové služby s retrained machine learning modelu. Aktualizační [aktivitu zdrojů](update-machine-learning-models.md) můžete použít k aktualizaci webové služby s nově trénovaným modelem.  

Podrobnosti o těchto aktivitách strojového učení najdete v [tématu Použití aktivit strojového učení.](transform-data-using-machine-learning.md) 

### <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Aktivitu uložené procedury serveru SQL Server v kanálu data factory můžete vyvolat uloženou proceduru v jednom z následujících úložišť dat: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database ve vašem podniku nebo virtuální počítač Azure. Podrobnosti najdete v článku [Aktivita uložené procedury.](transform-data-using-stored-procedure.md)  

### <a name="data-lake-analytics-u-sql-activity"></a>Aktivita U-SQL služby Data Lake Analytics
Aktivita U-SQL služby Data Lake Analytics spouští skript U-SQL v clusteru Azure Data Lake Analytics. Podrobnosti najdete v článku [aktivity U-SQL analýzy dat.](transform-data-using-data-lake-analytics.md) 

### <a name="databricks-notebook-activity"></a>Aktivita poznámkového bloku Databricks

Aktivita poznámkového bloku Azure Databricks v kanálu datové továrny spouští poznámkový blok Databricks v pracovním prostoru Azure Databricks. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Viz [Transformace dat spuštěním poznámkového bloku Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks Jar činnost

Aktivita Azure Databricks Jar v kanálu datové továrny spouští Spark Jar ve vašem clusteru Azure Databricks. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. V [tématu Transformace dat spuštěním aktivity Jar v Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Aktivita Databricks Python

Aktivita Pythonu Azure Databricks v kanálu datové továrny spouští soubor Pythonu v clusteru Azure Databricks. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Přečtěte [si, že v Azure Databricks najdete informace o transformaci dat spuštěním aktivity Pythonu.](transform-data-databricks-python.md)

### <a name="custom-activity"></a>Vlastní aktivita
Pokud potřebujete transformovat data způsobem, který není podporován data factory, můžete vytvořit vlastní aktivitu s vlastní logiku zpracování dat a použít aktivitu v kanálu. Vlastní aktivitu rozhraní .NET můžete nakonfigurovat tak, aby se spouštěla pomocí služby Azure Batch nebo clusteru Azure HDInsight. Podrobnosti najdete v článku Použití vlastního článku [aktivit.](transform-data-using-dotnet-custom-activity.md) 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Výpočetní prostředí
Vytvoříte propojenou službu pro výpočetní prostředí a pak použijete propojenou službu při definování transformační aktivity. Existují dva typy výpočetních prostředí podporovaných Data Factory. 

- **On-Demand**: V tomto případě je výpočetní prostředí plně spravováno společností Data Factory. Je automaticky vytvořen službou Data Factory před odesláním úlohy ke zpracování dat a odebrána po dokončení úlohy. Můžete nakonfigurovat a řídit podrobné nastavení výpočetního prostředí na vyžádání pro provádění úloh, správu clusteru a zaváděcí akce. 
- **Přineste si vlastní**: V tomto případě můžete zaregistrovat vlastní výpočetní prostředí (například HDInsight cluster) jako propojené služby v datové továrně. Výpočetní prostředí spravujete vy a služba Data Factory ho používá k provádění aktivit. 

Informace o výpočetních službách podporovaných službou Data Factory najdete v článku Výpočetní [propojené služby.](compute-linked-services.md) 

## <a name="next-steps"></a>Další kroky
Příklad použití transformační aktivity: Transformace [dat pomocí Sparku](tutorial-transform-data-spark-powershell.md) naleznete v následujícím kurzu.
