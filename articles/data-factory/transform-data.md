---
title: Transformace dat pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak transformovat data nebo zpracovávat data v Azure Data Factory pomocí Hadoopu, Machine Learning nebo Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: douglasl
ms.openlocfilehash: 09b5f387b0c7f765ea0835dcef79932203d68338
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014348"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat ve službě Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight streamování](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Uložená procedura](transform-data-using-stored-procedure.md)
> * [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Poznámkového bloku Databricks](transform-data-databricks-notebook.md)
> * [Soubor Jar Databricks](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [Vlastní rozhraní .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Přehled
Tento článek vysvětluje, aktivity transformace dat ve službě Azure Data Factory můžete použít k transformaci a zpracování nezpracovaných dat do předpovědi a přehledy. Aktivita transformace se spouští ve výpočetním prostředí, jako je cluster Azure HDInsight nebo Azure Batch. Poskytuje odkazy na články s podrobnými informacemi o každé aktivity transformace.

Data Factory podporuje následující aktivity transformace dat, které mohou být přidány do [kanály](concepts-pipelines-activities.md) buď jednotlivě nebo ve spojení s jinou aktivitou.

## <a name="hdinsight-hive-activity"></a>Aktivita HDInsight Hive
Aktivita HDInsight Hive v kanálu služby Data Factory provádí dotazy Hive sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivitu Hivu](transform-data-using-hadoop-hive.md) , kde najdete podrobnosti o této aktivitě. 

## <a name="hdinsight-pig-activity"></a>Aktivita Pig s HDInsight
Aktivita Pig s HDInsight v kanálech Data Factory spustí dotazy Pig sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita Pig](transform-data-using-hadoop-pig.md) , kde najdete podrobnosti o této aktivitě. 

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce s HDInsight
Aktivita HDInsight MapReduce v kanálech Data Factory provádí programů MapReduce sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita MapReduce](transform-data-using-hadoop-map-reduce.md) , kde najdete podrobnosti o této aktivitě.

## <a name="hdinsight-streaming-activity"></a>Aktivita HDInsight streamování
Aktivita HDInsight streamování v kanálech Data Factory provádí streamování Hadoop programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita HDInsight streamování](transform-data-using-hadoop-streaming.md) podrobnosti o této aktivitě.

## <a name="hdinsight-spark-activity"></a>Aktivita HDInsight Spark
HDInsight Spark aktivitu v kanálu služby Data Factory provádí programů Spark v clusteru HDInsight. Podrobnosti najdete v tématu [vyvolání programů Spark ze služby Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Aktivity Machine Learning
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikované webové služby Azure Machine Learning pro prediktivní analýzy. Použití [aktivita provedení dávky služby](transform-data-using-machine-learning.md) v kanálu Azure Data Factory, vyvoláte webovou službu Machine Learning k vytváření predikcí na data ve službě batch.

V průběhu času prediktivních modelů ve službě Machine Learning, experimenty vyhodnocování musí být retrained pomocí nové vstupní datové sady. Jakmile budete hotovi s přetrénování, budete chtít aktualizovat hodnoticí webové služby s modelem Machine Learning retrained. Můžete použít [aktivita prostředku aktualizace](update-machine-learning-models.md) jak aktualizovat webovou službu s nově trénovaného modelu.  

Zobrazit [aktivit Machine learningu pomocí](transform-data-using-machine-learning.md) podrobné informace o těchto aktivit Machine learningu. 

## <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Aktivita uložená procedura SQL serveru v kanálech Data Factory můžete vyvolat uloženou proceduru v jednom z následujících úložiště dat: Azure SQL Database, Azure SQL Data Warehouse, databáze SQL serveru ve vašem podniku nebo Virtuálním počítači Azure. Zobrazit [aktivity uložené procedury](transform-data-using-stored-procedure.md) , kde najdete podrobnosti.  

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita Data Lake Analytics U-SQL
Aktivita data Lake Analytics U-SQL spouští skript U-SQL v clusteru služby Azure Data Lake Analytics. Zobrazit [aktivita Data Analytics U-SQL](transform-data-using-data-lake-analytics.md) , kde najdete podrobnosti. 

## <a name="databricks-notebook-activity"></a>Aktivitu poznámkového bloku Databricks

Aktivity poznámkového bloku Azure Databricks v datové továrně kanál spuštění poznámkového bloku Databricks v pracovním prostoru Azure Databricks. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Zobrazit [transformovat data spuštěním poznámkového bloku Databricks](transform-data-databricks-notebook.md).

## <a name="databricks-jar-activity"></a>Aktivita Databricks Jar

Na Azure Databricks Jar aktivitu v kanálu služby Data Factory běží v clusteru Azure Databricks Spark Jar. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Zobrazit [transformovat data spuštěním Jar aktivit v Azure Databricks](transform-data-databricks-jar.md).

## <a name="databricks-python-activity"></a>Aktivita Databricks Pythonu

Azure Databricks Python aktivitu v kanálu služby Data Factory soubor Pythonu běží v clusteru Azure Databricks. Azure Databricks je spravovaná platforma pro spouštění Apache Spark. Zobrazit [transformovat data spuštěním aktivity Python v Azure Databricks](transform-data-databricks-python.md).

## <a name="custom-activity"></a>Vlastní aktivita
Pokud potřebujete transformovat data způsobem, který není podporován službou Data Factory, můžete vytvořit vlastní aktivitu s vlastní logiku zpracování dat a použijte aktivitu v kanálu. Můžete vytvořit vlastní aktivitu .NET pro spuštění pomocí služby Azure Batch nebo cluster Azure HDInsight. Zobrazit [použití vlastních aktivit](transform-data-using-dotnet-custom-activity.md) , kde najdete podrobnosti. 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Výpočetní prostředí
Vytvoření propojené služby pro výpočetní prostředí a pak použít propojené služby pro definování aktivit transformace. Existují dva typy výpočetních prostředí podporovaných službou Data Factory. 

- **Na vyžádání**:  V takovém případě se službou Data Factory plně spravované výpočetní prostředí. Je automaticky vytvořen ve službě Data Factory předtím, než je úlohy odeslané do dat procesu a odebrat, pokud je úloha dokončena. Můžete konfigurovat a řídit granulární nastavení výpočetní prostředky na vyžádání prostředí pro provádění úloh, správu clusteru a spuštění akce. 
- **Funkce přineste si vlastní**: V takovém případě mohou registrovat své vlastní výpočetní prostředí (například cluster HDInsight) jako propojenou službu ve službě Data Factory. Výpočetní prostředí spravujete sami a služba Data Factory používá je ke spuštění aktivity. 

Zobrazit [propojené výpočetní služby](compute-linked-services.md) článku se dozvíte o výpočetních služeb podporovaných službou Data Factory. 

## <a name="next-steps"></a>Další postup
Najdete v kurzu následující příklad pomocí aktivit transformace: [Kurz: transformace dat pomocí Sparku](tutorial-transform-data-spark-powershell.md)
