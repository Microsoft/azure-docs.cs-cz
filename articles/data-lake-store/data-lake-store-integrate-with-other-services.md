---
title: Integrace Data Lake Storage Gen1 s dalšími službami Azure
description: Naučte se, jak můžete integrovat Azure Data Lake Storage Gen1 s dalšími službami Azure.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9318ce7d73f2d05dde6815a0ded4269510760251
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441982"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrace Azure Data Lake Storage Gen1 s dalšími službami Azure
Azure Data Lake Storage Gen1 lze použít ve spojení s jinými službami Azure a povolit širší škálu scénářů. Následující článek obsahuje seznam služeb, které Data Lake Storage Gen1 lze integrovat s nástrojem.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Použití Data Lake Storage Gen1 se službou Azure HDInsight
Můžete zřídit cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) , který používá data Lake Storage Gen1 jako úložiště vyhovující HDFS. V této verzi můžete pro clustery Hadoop a v systému Windows a Linux použít Data Lake Storage Gen1 jenom jako další úložiště. Tyto clustery stále používají jako výchozí úložiště Azure Storage (WASB). U clusterů HBA v systému Windows a Linux ale můžete použít Data Lake Storage Gen1 jako výchozí úložiště nebo další úložiště nebo obojí.

Pokyny, jak zřídit cluster HDInsight s Data Lake Storage Gen1, najdete tady:

* [Zřízení clusteru HDInsight s Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Zřízení clusteru HDInsight s Data Lake Storage Gen1 jako výchozím úložiště pomocí Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Zřízení clusteru HDInsight s Data Lake Storage Gen1 jako další úložiště pomocí Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Použití Data Lake Storage Gen1 s Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) vám umožní pracovat s velkým objemem dat v cloudovém měřítku. Dynamicky zřizuje prostředky a umožňuje provádět analýzy terabajtů nebo dokonce i Exabyte dat, která se dají ukládat v několika podporovaných zdrojích dat, přičemž jedna z nich je Data Lake Storage Gen1. Data Lake Analytics je speciálně optimalizovaná tak, aby spolupracovala s Data Lake Storage Gen1 a poskytovala nejvyšší úroveň výkonu, propustnosti a paralelismu pro úlohy s velkými objemy dat.

Pokyny, jak používat Data Lake Analytics s Data Lake Storage Gen1, najdete v tématu Začínáme [s Data Lake Analytics pomocí Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Použití Data Lake Storage Gen1 s Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) můžete použít k ingestování dat z tabulek Azure, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage blobů a místních databází. V ekosystému Azure je první občan třídy, Azure Data Factory lze použít k orchestraci přijímání dat z těchto zdrojů do Data Lake Storage Gen1.

Pokyny, jak používat Azure Data Factory s Data Lake Storage Gen1, najdete v tématu [přesun dat do a z Data Lake Storage Gen1 pomocí Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1
Azure Data Lake Storage Gen1 poskytuje nástroj příkazového řádku AdlCopy, který umožňuje kopírovat data z Azure Blob Storage na účet Data Lake Storage Gen1. Další informace najdete v tématu [kopírování dat z objektů blob Azure Storage do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopírování dat mezi Azure SQL Database a Data Lake Storage Gen1
Apache Sqoop můžete použít k importu a exportu dat mezi Azure SQL Database a Data Lake Storage Gen1. Další informace najdete v tématu [kopírování dat mezi Data Lake Storage Gen1 a Azure SQL Database pomocí Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Použití Data Lake Storage Gen1 s Stream Analytics
Data Lake Storage Gen1 můžete použít jako jeden z výstupů k ukládání dat streamování pomocí Azure Stream Analytics. Další informace najdete v tématu [streamování dat z Azure Storage blob do data Lake Storage Gen1 pomocí Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Použití Data Lake Storage Gen1 s Power BI
Pomocí Power BI můžete importovat data z účtu Data Lake Storage Gen1 a analyzovat je a vizualizovat data. Další informace najdete v tématu [Analýza dat v Data Lake Storage Gen1 pomocí Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Použití Data Lake Storage Gen1 s Data Catalog
Data z Data Lake Storage Gen1 můžete zaregistrovat do Azure Data Catalog, aby byla data zjistitelná v celé organizaci. Další informace najdete [v tématu Registrace dat z Data Lake Storage Gen1 v Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Použití Data Lake Storage Gen1 s služba SSIS (SQL Server Integration Services) (SSIS)
K připojení balíčku SSIS s Data Lake Storage Gen1 můžete použít Správce připojení Data Lake Storage Gen1 v SSIS. Další informace najdete v tématu [použití Data Lake Storage Gen1 s SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Použití Data Lake Storage Gen1 se službou Azure synapse Analytics
K načtení dat z Data Lake Storage Gen1 do služby Azure synapse Analytics (dřív SQL Data Warehouse) můžete použít základnu. Další informace najdete v tématu [použití Data Lake Storage Gen1 s analýzou Azure synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Použití Data Lake Storage Gen1 s využitím Azure Event Hubs
Pomocí Azure Data Lake Storage Gen1 můžete archivovat a zachytit data přijatá službou Azure Event Hubs. Další informace najdete v tématu [použití Data Lake Storage Gen1 s využitím Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme s Data Lake Storage Gen1 pomocí portálu](data-lake-store-get-started-portal.md)
* [Začínáme s Data Lake Storage Gen1 pomocí prostředí PowerShell](data-lake-store-get-started-powershell.md)  

