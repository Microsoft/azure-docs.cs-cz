---
title: Transformace dat pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak k transformaci dat nebo zpracování dat v Azure Data Factory pomocí Hadoop, Machine Learning nebo Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 4915e8aebf110b5ae799f409472b43b986a18b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat v Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Streamování Hadoop](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Uložená procedura](transform-data-using-stored-procedure.md)
> * [U-SQL Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Vlastní rozhraní .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Přehled
Tento článek vysvětluje aktivit transformace dat v Azure Data Factory můžete použít k transformaci a zpracuje nezpracovaná data do předpovědi a statistiky. Transformace aktivity spustí výpočetní prostředí jako je cluster Azure HDInsight nebo Azure Batch. Poskytuje odkazy na články s podrobné informace o každé aktivity transformace.

Objekt pro vytváření dat podporuje následující aktivit transformace dat, které mohou být přidány do [kanály](concepts-pipelines-activities.md) buď jednotlivě nebo zřetězené s jinou aktivitou.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivit transformace v datové továrně verze 1](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>Aktivitu HDInsight Hive
Aktivity HDInsight Hive v kanálu pro vytváření dat provede dotazů Hive sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [aktivitu Hivu](transform-data-using-hadoop-hive.md) článku podrobnosti o této aktivitě. 

## <a name="hdinsight-pig-activity"></a>Aktivita HDInsight Pig
HDInsight Pig aktivity v kanálu pro vytváření dat provede Pig dotazy na vlastní nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [vepřových aktivity](transform-data-using-hadoop-pig.md) článku podrobnosti o této aktivitě. 

## <a name="hdinsight-mapreduce-activity"></a>Činnost MapReduce s HDInsight
Činnost HDInsight MapReduce v objektu pro vytváření dat kanál provede MapReduce programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [činnost MapReduce](transform-data-using-hadoop-map-reduce.md) článku podrobnosti o této aktivitě.

## <a name="hdinsight-streaming-activity"></a>HDInsight streamované aktivitě
HDInsight streamování aktivity v kanálu pro vytváření dat provede streamování Hadoop programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. V tématu [HDInsight streamované aktivitě](transform-data-using-hadoop-streaming.md) podrobnosti o této aktivitě.

## <a name="hdinsight-spark-activity"></a>Aktivita HDInsight Spark
Aktivity HDInsight Spark v objektu pro vytváření dat kanál provede na clusteru HDInsight Spark programy. Podrobnosti najdete v tématu [vyvolání Spark programy z Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning aktivity
Azure Data Factory můžete snadno vytvořit kanály, které používají publikované webové služby Azure Machine Learning pro prediktivní analýzy. Pomocí [aktivita provedení dávky](transform-data-using-machine-learning.md) v kanál služby Azure Data Factory můžete vyvolat webové služby Machine Learning k provádět předpovědi na datech v dávce.

V průběhu času prediktivní modely v Machine Learning vyhodnocování experimentů muset být retrained pomocí nové vstupní datové sady. Jakmile jste hotovi s přeučení chcete aktualizovat webovou službu vyhodnocování s modelem retrained Machine Learning. Můžete použít [aktivita prostředku aktualizace](update-machine-learning-models.md) aktualizace webové služby s nově naučeného modelu.  

V tématu [použití Machine Learning aktivity](transform-data-using-machine-learning.md) podrobnosti o tyto aktivity Machine Learning. 

## <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Můžete aktivity uložené procedury serveru SQL v objektu pro vytváření dat kanál vyvolat uloženou proceduru v jednom z následujících úložišť dat: databáze SQL Azure, Azure SQL Data Warehouse, databáze SQL serveru ve vašem podniku nebo virtuální počítač Azure. V tématu [aktivity uložené procedury](transform-data-using-stored-procedure.md) článku.  

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita Data Lake Analytics U-SQL
Data Lake Analytics U-SQL aktivity spouští skript U-SQL na clusteru služby Azure Data Lake Analytics. V tématu [Data Analytics U-SQL aktivity](transform-data-using-data-lake-analytics.md) článku. 

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
Pokud potřebujete transformovat data způsobem, který není podporován službou Data Factory, můžete vytvořit vlastní aktivity s logika zpracování dat a použijte aktivitu v kanálu. Můžete nakonfigurovat vlastní .NET aktivity ke spuštění pomocí služby Azure Batch nebo clusteru Azure HDInsight. V tématu [použít vlastní aktivity](transform-data-using-dotnet-custom-activity.md) článku. 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Výpočetní prostředí
Vytvoření propojené služby pro výpočetní prostředí a potom pomocí propojené služby, při definování aktivitou transformace. Existují dva typy výpočetní prostředí podporovaných službou Data Factory. 

- **Na vyžádání**: V tomto případě je plně spravovaná výpočetní prostředí službou Data Factory. Je vytvořeno automaticky pomocí služby Data Factory předtím, než je úloha odeslána zpracování dat a odebrat při dokončení úlohy. Můžete konfigurovat a řídit granulární nastavení na vyžádání výpočetní prostředí pro spuštění úlohy, správu clusteru a zavádění akce. 
- **Přineste si vlastní**: V tomto případě můžete zaregistrovat vlastní výpočetní prostředí (například cluster HDInsight) jako propojené služby ve službě Data Factory. Výpočetní prostředí spravujete vy a služba Data Factory používá, je spuštění aktivity. 

V tématu [propojené výpočetní služby](compute-linked-services.md) článku Další informace o výpočetní služby podporovaných službou Data Factory. 

## <a name="next-steps"></a>Další postup
Projděte si následující kurz pro příklad použití aktivitou transformace: [kurz: transformace dat pomocí Spark](tutorial-transform-data-spark-powershell.md)