---
title: Transformace dat
description: Transformujte data nebo zpracujte data v Azure Data Factory pomocí Hadoop, Azure Machine Learning Studio (Classic) nebo Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 37eac4acab7232e44f94e852b1c04c5549447b09
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637679"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat v Azure Data Factory

> [!div class="op_single_selector"]
> * [Mapování toku dat](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Streamování HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (klasický)](transform-data-using-machine-learning.md) 
> * [Uložená procedura](transform-data-using-stored-procedure.md)
> * [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Poznámkový blok datacihly](transform-data-databricks-notebook.md)
> * [JAR pro datacihly](transform-data-databricks-jar.md)
> * [Python datacihly](transform-data-databricks-python.md)
> * [Vlastní rozhraní .NET](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Přehled
Tento článek vysvětluje aktivity transformace dat v Azure Data Factory, které můžete použít k transformaci a zpracování nezpracovaných dat do předpovědi a přehledů ve velkém měřítku. Transformační aktivita se spouští v výpočetním prostředí, jako je Azure Databricks nebo Azure HDInsight. Obsahuje odkazy na články s podrobnými informacemi o každé aktivitě transformace.

Data Factory podporuje následující aktivity transformace dat, které je možné přidat do [kanálů](concepts-pipelines-activities.md) jednotlivě nebo zřetězit s jinou aktivitou.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Nativně transformovat v Azure Data Factory s využitím toků dat

### <a name="mapping-data-flows"></a>Toky dat mapování

Mapování datových toků je vizuálně navržené transformace dat v Azure Data Factory. Datové toky umožňují technikům pro transformaci dat pracovat s grafickými logikami bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci Azure Data Factory kanálů, které používají clustery Spark se škálováním na více systémů. Aktivity toku dat je možné provozovat prostřednictvím stávajících Data Factory plánování, řízení, toku a monitorování. Další informace najdete v tématu [mapování toků dat](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Toky dat tahání

Tahání datové toky v Azure Data Factory umožňují iterativní přípravu dat bez kódu na úrovni cloudu. Tahání datové toky se integrují s [Power Query online](/power-query/) a zpřístupňují Power Query M funkcí pro data tahání v cloudovém měřítku prostřednictvím spuštění Sparku. Další informace najdete v tématu [toky tahání data](wrangling-data-flow-overview.md)flows.

## <a name="external-transformations"></a>Externí transformace

Volitelně můžete ručně transformovat kód a spravovat externí výpočetní prostředí.

### <a name="hdinsight-hive-activity"></a>Aktivita v podregistru HDInsight
Aktivita podregistru HDInsight v Data Factoryovém kanálu spouští dotazy na podregistry ve vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání. Podrobnosti o této aktivitě najdete v článku o [aktivitě podregistru](transform-data-using-hadoop-hive.md) . 

### <a name="hdinsight-pig-activity"></a>Aktivita prasete v HDInsight
Aktivita prasete v HDInsight v kanálu Data Factory spouští dotazy na prasaty na vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání. Podrobnosti o této aktivitě najdete v článku o [aktivitě prasete](transform-data-using-hadoop-pig.md) . 

### <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce HDInsight
Aktivita MapReduce služby HDInsight v kanálu Data Factory spouští programy MapReduce na vašem clusteru HDInsight založeném na systému Windows nebo Linux na vyžádání. Podrobnosti o této aktivitě najdete v článku o [aktivitě MapReduce](transform-data-using-hadoop-map-reduce.md) .

### <a name="hdinsight-streaming-activity"></a>Aktivita streamování HDInsight
Aktivita streamování HDInsight v kanálu Data Factory spouští programy pro streamování Hadoop na vašem clusteru HDInsight založeném na systému Windows/Linux nebo na vyžádání. Podrobnosti o této aktivitě najdete v tématu [aktivita streamování HDInsight](transform-data-using-hadoop-streaming.md) .

### <a name="hdinsight-spark-activity"></a>Aktivita Spark HDInsight
Aktivita HDInsight Spark v kanálu Data Factory spouští programy Spark ve vlastním clusteru HDInsight. Podrobnosti najdete v tématu [vyvolání programů Spark z Azure Data Factory](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Aktivity Azure Machine Learning Studio (Classic)
Azure Data Factory vám umožní snadno vytvářet kanály, které používají publikovanou webovou službu Azure Machine Learning Studio (Classic) pro prediktivní analýzy. Pomocí [aktivity dávkového spuštění](transform-data-using-machine-learning.md) v kanálu Azure Data Factory můžete vyvolat webovou službu studia (Classic), která předpovědi data ve službě Batch.

Prediktivní modely v studiu bodování studia (Classic) se v průběhu času musí přenášet pomocí nových vstupních datových sad. Až budete s rekurzem hotovi, chcete aktualizovat webovou službu bodování pomocí převýukového modelu Machine Learning. [Aktivitu aktualizovat prostředek](update-machine-learning-models.md) můžete použít k aktualizaci webové služby pomocí nově vyučeného modelu.  

Podrobnosti o těchto aktivitách studia (Classic) najdete v tématu [použití aktivit Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md) . 

### <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Pomocí aktivity uložená procedura SQL Server v kanálu Data Factory můžete vyvolat uloženou proceduru v jednom z následujících úložišť dat: Azure SQL Database, Azure synapse Analytics (dříve SQL Data Warehouse), SQL Server databáze ve vašem podniku nebo na virtuálním počítači Azure. Podrobnosti najdete v článku [aktivita uložených procedur](transform-data-using-stored-procedure.md) .  

### <a name="data-lake-analytics-u-sql-activity"></a>Aktivita Data Lake Analytics U-SQL
Aktivita Data Lake Analytics U-SQL spouští skript U-SQL v clusteru Azure Data Lake Analytics. Podrobnosti najdete v článku o [aktivitě data Analytics U-SQL](transform-data-using-data-lake-analytics.md) . 

### <a name="databricks-notebook-activity"></a>Aktivita poznámkového bloku datacihly

Aktivita poznámkového bloku Azure Databricks v kanálu Data Factory spustí v pracovním prostoru Azure Databricks Poznámkový blok datacihly. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Viz [transformaci dat spuštěním poznámkového bloku datacihly](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Aktivita JAR pro datacihly

Aktivita Azure Databricks jar v kanálu Data Factory spouští Sparkovou sklenici v clusteru Azure Databricks. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Viz [transformace dat spuštěním aktivity jar v Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Aktivita v Pythonu datacihly

Aktivita Azure Databricks Pythonu v kanálu Data Factory spustí v clusteru Azure Databricks soubor Pythonu. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Viz [transformace dat spuštěním aktivity Pythonu v Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Vlastní aktivita
Pokud potřebujete transformovat data způsobem, který není podporován Data Factory, můžete vytvořit vlastní aktivitu s vlastní logikou zpracování dat a použít aktivitu v kanálu. Vlastní aktivitu rozhraní .NET můžete nakonfigurovat tak, aby běžela buď pomocí služby Azure Batch, nebo clusteru Azure HDInsight. Podrobnosti najdete v článku [použití vlastních aktivit](transform-data-using-dotnet-custom-activity.md) . 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Výpočetní prostředí
Vytvoříte propojenou službu pro výpočetní prostředí a pak při definování aktivity transformace použijete propojenou službu. Data Factory podporují dva typy výpočetních prostředí. 

- **Na vyžádání** : v tomto případě je výpočetní prostředí plně spravované pomocí Data Factory. Služba Data Factory ji automaticky vytvoří před odesláním úlohy ke zpracování dat a po jejím dokončení se odstraní. Můžete nakonfigurovat a řídit podrobné nastavení výpočetního prostředí na vyžádání pro provádění úloh, správu clusterů a spouštěcí akce. 
- **Přineste si vlastní** : v takovém případě můžete zaregistrovat vlastní výpočetní prostředí (například cluster HDInsight) jako propojenou službu v Data Factory. Výpočetní prostředí je spravováno vámi a služba Data Factory ji používá ke spuštění aktivit. 

Seznamte se s výpočetními službami podporovanými nástrojem Data Factory v článku věnovaném [výpočetním propojeným službám](compute-linked-services.md) . 

## <a name="next-steps"></a>Další kroky
V následujícím kurzu najdete příklad použití transformační aktivity: [kurz: transformace dat pomocí Sparku](tutorial-transform-data-spark-powershell.md) .