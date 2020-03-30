---
title: Integrace Azure Data Lake Storage Gen1 s dalšími službami Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak se Azure Data Lake Storage Gen1 integruje s dalšími službami Azure
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 43024b63a355646f607adbb6623cc6c349374ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535591"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrace Azure Data Lake Storage Gen1 s dalšími službami Azure
Azure Data Lake Storage Gen1 se dá použít ve spojení s dalšími službami Azure, které umožňují širší škálu scénářů. V následujícím článku jsou uvedeny služby, které data lake storage Gen1 lze integrovat s.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Použití data lake storage Gen1 s Azure HDInsight
Můžete zřídit cluster [Azure HDInsight,](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) který používá Data Lake Storage Gen1 jako úložiště kompatibilní s HDFS. Pro tuto verzi pro clustery Hadoop a Storm ve Windows a Linuxu můžete použít data Lake Storage Gen1 pouze jako další úložiště. Tyto clustery stále používají Azure Storage (WASB) jako výchozí úložiště. Pro clustery HBase ve Windows a Linuxu však můžete použít Data Lake Storage Gen1 jako výchozí úložiště nebo další úložiště nebo obojí.

Pokyny k zřizování clusteru HDInsight pomocí data lake storage gen1 najdete v tématu:

* [Zřízení clusteru HDInsight s gen1 úložiště datového jezera pomocí portálu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Zřízení clusteru HDInsight s gen1 úložiště datového jezera jako výchozím úložištěm pomocí Azure PowerShellu](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Zřízení clusteru HDInsight s Gen1 úložiště datového jezera jako další úložiště pomocí Azure PowerShellu](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Použití data lake storage gen1 s Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) vám umožňuje pracovat s velkými objemy dat v cloudovém měřítku. Dynamicky zřcuje prostředky a umožňuje analýzy terabajtů nebo dokonce exabajtů dat, které mohou být uloženy v řadě podporovaných zdrojů dat, z toho jeden z nich je Data Lake Storage Gen1. Data Lake Analytics je speciálně optimalizovaná pro práci s Data Lake Storage Gen1 – poskytuje nejvyšší úroveň výkonu, propustnost a paralelizaci pro velké datové úlohy.

Pokyny, jak používat Data Lake Analytics s Date Lake Storage Gen1, najdete v [tématu Začínáme s analýzou datových jezer pomocí služby Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Použití data lake storage Gen1 s Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) můžete použít k ingestování dat z tabulek Azure, Azure SQL Database, Azure SQL DataWarehouse, objektů Blob s úložištěm Azure a místních databází. Jako prvotřídní občan v ekosystému Azure, Azure Data Factory se dá použít k orchestraci ingestování dat z těchto zdrojů do Data Lake Storage Gen1.

Pokyny, jak používat Azure Data Factory s Date Lake Storage Gen1, najdete v tématu [Přesun dat do a z Data Lake Storage Gen1 pomocí Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1
Azure Data Lake Storage Gen1 poskytuje nástroj příkazového řádku, AdlCopy, který umožňuje kopírovat data z Azure Blob Storage do účtu Data Lake Storage Gen1. Další informace najdete [v tématu Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopírování dat mezi Databází Azure SQL a úložištěm datových jezer Gen1
Apache Sqoop můžete použít k importu a exportu dat mezi Azure SQL Database a Data Lake Storage Gen1. Další informace najdete [v tématu Kopírování dat mezi Data Lake Storage Gen1 a Azure SQL databáze pomocí Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Použití data lake storage gen1 s Stream Analytics
K ukládání dat streamovaných dat pomocí služby Data Lake Storage Gen1 můžete použít jeden z výstupů pomocí Azure Stream Analytics. Další informace najdete v tématu [Streamování dat z objektu blob úložiště Azure do data Lake Storage Gen1 pomocí Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Použití úložiště datového jezera Gen1 s Power BI
Pomocí Power BI můžete data z účtu Data Lake Storage Gen1 importovat k analýze a vizualizaci dat. Další informace najdete [v tématu Analýza dat v aplikaci Data Lake Storage Gen1 pomocí Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Použití úložiště datových jezer Gen1 s katalogem dat
Data z modulu Data Lake Storage Gen1 můžete zaregistrovat do katalogu dat Azure, aby byla data zjistitelná v celé organizaci. Další informace najdete [v tématu Registrace dat z modulu Data Lake Storage Gen1 v Katalogu dat Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Použití úložiště datového jezera Gen1 se službami integrace serveru SQL Server (SSIS)
Správce připojení Data Lake Storage Gen1 v SSIS můžete použít k připojení balíčku SSIS s Date Lake Storage Gen1. Další informace naleznete v [tématu Použití úložiště datového jezera Gen1 s SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Použití data lake storage gen1 s datovým skladem SQL
PolyBase můžete použít k načtení dat z Data Lake Storage Gen1 do datového skladu SQL. Další informace naleznete v [tématu Použití úložiště datového jezera Gen1 s sql datový sklad](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Použití data lake storage gen1 s Azure Event Hubs
Azure Data Lake Storage Storage Gen1 můžete použít k archivaci a sběru dat přijatých v Azure Event Hubs. Další informace najdete [v tématu Použití data lake storage Gen1 s Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme s úložištěm datových jezer Gen1 pomocí portálu](data-lake-store-get-started-portal.md)
* [Začínáme s prostředím Data Lake Storage Gen1 pomocí PowerShellu](data-lake-store-get-started-powershell.md)  

