---
title: 'Transformaci dat: Proces & transformace dat | Microsoft Docs'
description: Zjistěte, jak k transformaci dat nebo zpracování dat v Azure Data Factory pomocí Hadoop, Machine Learning nebo Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: afa00a81738563ec3c5b4b168f533b732c263825
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045615"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat v Azure Data Factory
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
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [aktivit transformace dat v datové továrně](../transform-data.md).

Tento článek vysvětluje aktivit transformace dat v Azure Data Factory můžete použít k transformaci a zpracuje nezpracovaná data do předpovědi a statistiky. Transformace aktivity spustí výpočetní prostředí jako je cluster Azure HDInsight nebo Azure Batch. Poskytuje odkazy na články s podrobné informace o každé aktivity transformace.

Objekt pro vytváření dat podporuje následující aktivit transformace dat, které mohou být přidány do [kanály](data-factory-create-pipelines.md) buď jednotlivě nebo zřetězené s jinou aktivitou.

> [!NOTE]
> Návod s podrobné pokyny najdete v tématu [vytvoření kanálu s transformace Hive](data-factory-build-your-first-pipeline.md) článku.  
> 
> 

## <a name="hdinsight-hive-activity"></a>Aktivitu HDInsight Hive
Aktivity HDInsight Hive v kanálu pro vytváření dat provede dotazů Hive sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [Hive aktivity](data-factory-hive-activity.md) článku podrobnosti o této aktivitě. 

## <a name="hdinsight-pig-activity"></a>Aktivita HDInsight Pig
HDInsight Pig aktivity v kanálu pro vytváření dat provede Pig dotazy na vlastní nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [Pig aktivity](data-factory-pig-activity.md) článku podrobnosti o této aktivitě. 

## <a name="hdinsight-mapreduce-activity"></a>Činnost MapReduce s HDInsight
Činnost HDInsight MapReduce v objektu pro vytváření dat kanál provede MapReduce programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [činnost MapReduce](data-factory-map-reduce.md) článku podrobnosti o této aktivitě.

## <a name="hdinsight-streaming-activity"></a>HDInsight streamované aktivitě
HDInsight streamované aktivitě v objektu pro vytváření dat kanál provede streamování Hadoop programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [HDInsight streamované aktivitě](data-factory-hadoop-streaming-activity.md) podrobnosti o této aktivitě.

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
Aktivity HDInsight Spark v objektu pro vytváření dat kanál provede na clusteru HDInsight Spark programy. Podrobnosti najdete v tématu [vyvolání Spark programy z Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning aktivity
Azure Data Factory můžete snadno vytvořit kanály, které používají publikované webové služby Azure Machine Learning pro prediktivní analýzy. Pomocí [aktivita provedení dávky](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) v kanál služby Azure Data Factory můžete vyvolat webové služby Machine Learning k provádět předpovědi na datech v dávce.

V průběhu času prediktivní modely v Machine Learning vyhodnocování experimentů muset být retrained pomocí nové vstupní datové sady. Jakmile jste hotovi s přeučení chcete aktualizovat webovou službu vyhodnocování s modelem retrained Machine Learning. Můžete použít [aktivita prostředku aktualizace](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) aktualizace webové služby s nově naučeného modelu.  

V tématu [použití Machine Learning aktivity](data-factory-azure-ml-batch-execution-activity.md) podrobnosti o tyto aktivity Machine Learning. 

## <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Můžete aktivity uložené procedury serveru SQL v objektu pro vytváření dat kanál vyvolat uloženou proceduru v jednom z následujících úložišť dat: databáze SQL Azure, Azure SQL Data Warehouse, databáze SQL serveru ve vašem podniku nebo virtuální počítač Azure. V tématu [aktivity uložené procedury](data-factory-stored-proc-activity.md) článku.  

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita Data Lake Analytics U-SQL
Data Lake Analytics U-SQL aktivity spouští skript U-SQL na clusteru služby Azure Data Lake Analytics. V tématu [Data Analytics U-SQL aktivity](data-factory-usql-activity.md) článku. 

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
Pokud potřebujete transformovat data způsobem, který není podporován službou Data Factory, můžete vytvořit vlastní aktivity s logika zpracování dat a použijte aktivitu v kanálu. Můžete nakonfigurovat vlastní .NET aktivity ke spuštění pomocí služby Azure Batch nebo clusteru Azure HDInsight. V tématu [použít vlastní aktivity](data-factory-use-custom-activities.md) článku. 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Výpočetní prostředí
Vytvoření propojené služby pro výpočetní prostředí a potom pomocí propojené služby, při definování aktivitou transformace. Existují dva typy výpočetní prostředí podporovaných službou Data Factory. 

1. **Na vyžádání**: V tomto případě je plně spravovaná výpočetní prostředí službou Data Factory. Je vytvořeno automaticky pomocí služby Data Factory předtím, než je úloha odeslána zpracování dat a odebrat při dokončení úlohy. Můžete konfigurovat a řídit granulární nastavení na vyžádání výpočetní prostředí pro spuštění úlohy, správu clusteru a zavádění akce. 
2. **Přineste si vlastní**: V tomto případě můžete zaregistrovat vlastní výpočetní prostředí (například cluster HDInsight) jako propojené služby ve službě Data Factory. Výpočetní prostředí spravujete vy a služba Data Factory používá, je spuštění aktivity. 

V tématu [propojené výpočetní služby](data-factory-compute-linked-services.md) článku Další informace o výpočetní služby podporovaných službou Data Factory. 

## <a name="summary"></a>Souhrn
Azure Data Factory podporuje následující aktivit transformace dat a výpočetním prostředí pro aktivity. Aktivity transformace můžete přidat do kanálů buď jednotlivě nebo zřetězené s jinou aktivitou.

| Aktivita transformace dat | Výpočetní prostředí |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streamování Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |Virtuální počítač Azure |
| [Uložená procedura](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse nebo SQL Server |
| [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] nebo Azure Batch |

