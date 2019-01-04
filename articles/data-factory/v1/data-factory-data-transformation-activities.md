---
title: 'Transformace dat: Proces a transformovat data | Dokumentace Microsoftu'
description: Zjistěte, jak transformovat data nebo zpracovávat data v Azure Data Factory pomocí Hadoopu, Machine Learning nebo Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b1a99f2872a69e01232c69a73f36319552429ca0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022440"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformace dat ve službě Azure Data Factory
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktivity transformace dat ve službě Data Factory](../transform-data.md).

Tento článek vysvětluje, aktivity transformace dat ve službě Azure Data Factory můžete použít k transformaci a zpracování nezpracovaných dat do předpovědi a přehledy. Aktivita transformace se spouští ve výpočetním prostředí, jako je cluster Azure HDInsight nebo Azure Batch. Poskytuje odkazy na články s podrobnými informacemi o každé aktivity transformace.

Data Factory podporuje následující aktivity transformace dat, které mohou být přidány do [kanály](data-factory-create-pipelines.md) buď jednotlivě nebo ve spojení s jinou aktivitou.

> [!NOTE]
> Návod s podrobnými pokyny najdete v tématu [vytvoření kanálu s transformace Hive](data-factory-build-your-first-pipeline.md) článku.  
> 
> 

## <a name="hdinsight-hive-activity"></a>Aktivita HDInsight Hive
Aktivita HDInsight Hive v kanálu služby Data Factory provádí dotazy Hive sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita Hive](data-factory-hive-activity.md) , kde najdete podrobnosti o této aktivitě. 

## <a name="hdinsight-pig-activity"></a>Aktivita Pig s HDInsight
Aktivita Pig s HDInsight v kanálech Data Factory spustí dotazy Pig sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita Pig](data-factory-pig-activity.md) , kde najdete podrobnosti o této aktivitě. 

## <a name="hdinsight-mapreduce-activity"></a>Aktivita MapReduce s HDInsight
Aktivita HDInsight MapReduce v kanálech Data Factory provádí programů MapReduce sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita MapReduce](data-factory-map-reduce.md) , kde najdete podrobnosti o této aktivitě.

## <a name="hdinsight-streaming-activity"></a>Aktivita HDInsight streamování
Aktivita HDInsight streamování v kanálech Data Factory provádí streamování Hadoop programy sami nebo clusteru HDInsight se systémem Windows nebo Linux na vyžádání. Zobrazit [aktivita HDInsight streamování](data-factory-hadoop-streaming-activity.md) podrobnosti o této aktivitě.

## <a name="hdinsight-spark-activity"></a>Aktivita Spark služby HDInsight
HDInsight Spark aktivitu v kanálu služby Data Factory provádí programů Spark v clusteru HDInsight. Podrobnosti najdete v tématu [vyvolání programů Spark ze služby Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Aktivity Machine Learning
Azure Data Factory umožňuje snadno vytvářet kanály, které používají publikované webové služby Azure Machine Learning pro prediktivní analýzy. Použití [aktivita provedení dávky služby](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) v kanálu Azure Data Factory, vyvoláte webovou službu Machine Learning k vytváření predikcí na data ve službě batch.

V průběhu času prediktivních modelů ve službě Machine Learning, experimenty vyhodnocování musí být retrained pomocí nové vstupní datové sady. Jakmile budete hotovi s přetrénování, budete chtít aktualizovat hodnoticí webové služby s modelem Machine Learning retrained. Můžete použít [aktivita prostředku aktualizace](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) jak aktualizovat webovou službu s nově trénovaného modelu.  

Zobrazit [aktivit Machine learningu pomocí](data-factory-azure-ml-batch-execution-activity.md) podrobné informace o těchto aktivit Machine learningu. 

## <a name="stored-procedure-activity"></a>Aktivita uložené procedury
Aktivita uložená procedura SQL serveru v kanálech Data Factory můžete vyvolat uloženou proceduru v jednom z následujících úložiště dat: Azure SQL Database, Azure SQL Data Warehouse, databáze SQL serveru ve vašem podniku nebo Virtuálním počítači Azure. Zobrazit [aktivity uložené procedury](data-factory-stored-proc-activity.md) , kde najdete podrobnosti.  

## <a name="data-lake-analytics-u-sql-activity"></a>Aktivita Data Lake Analytics U-SQL
Aktivita U-SQL služby data Lake Analytics spouští skript U-SQL v clusteru služby Azure Data Lake Analytics. Zobrazit [aktivita U-SQL služby Data Analytics](data-factory-usql-activity.md) , kde najdete podrobnosti. 

## <a name="net-custom-activity"></a>Vlastní aktivita .NET
Pokud potřebujete transformovat data způsobem, který není podporován službou Data Factory, můžete vytvořit vlastní aktivitu s vlastní logiku zpracování dat a použijte aktivitu v kanálu. Můžete vytvořit vlastní aktivitu .NET pro spuštění pomocí služby Azure Batch nebo cluster Azure HDInsight. Zobrazit [použití vlastních aktivit](data-factory-use-custom-activities.md) , kde najdete podrobnosti. 

Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Výpočetní prostředí
Vytvoření propojené služby pro výpočetní prostředí a pak použít propojené služby pro definování aktivit transformace. Existují dva typy výpočetních prostředí podporovaných službou Data Factory. 

1. **Na vyžádání**:  V takovém případě se službou Data Factory plně spravované výpočetní prostředí. Je automaticky vytvořen ve službě Data Factory předtím, než je úlohy odeslané do dat procesu a odebrat, pokud je úloha dokončena. Můžete konfigurovat a řídit granulární nastavení výpočetní prostředky na vyžádání prostředí pro provádění úloh, správu clusteru a spuštění akce. 
2. **Funkce přineste si vlastní**: V takovém případě mohou registrovat své vlastní výpočetní prostředí (například cluster HDInsight) jako propojenou službu ve službě Data Factory. Výpočetní prostředí spravujete sami a služba Data Factory používá je ke spuštění aktivity. 

Zobrazit [propojené výpočetní služby](data-factory-compute-linked-services.md) článku se dozvíte o výpočetních služeb podporovaných službou Data Factory. 

## <a name="summary"></a>Souhrn
Azure Data Factory podporuje následující aktivity transformace dat a výpočetní prostředí pro aktivity. Aktivity transformace můžete přidat do kanálů jednotlivě nebo zřetězená s jinou aktivitou.

| Aktivita transformace dat | Výpočetní prostředí |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streamování Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Aktivity Machine Learning: Dávkové spouštění a aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |Virtuální počítač Azure |
| [Uložená procedura](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse nebo SQL Server |
| [U-SQL Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] nebo Azure Batch |

