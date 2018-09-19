---
title: Integrace s dalšími službami Azure, Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Vysvětlení, jak Azure Data Lake Storage Gen1 integruje s dalšími službami Azure
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: e377a29167ace21e021568f3c65cefd1098d1dab
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127126"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrace s dalšími službami Azure, Azure Data Lake Storage Gen1
Azure Data Lake Storage Gen1 lze použít ve spojení s dalšími službami Azure umožňující širší škálu scénářů. V následujícím článku seznam služeb, které Gen1 úložiště Data Lake je možné integrovat se službou.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Azure HDInsight pomocí Data Lake Storage Gen1
Můžete zřídit [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) clusteru, který používá Data Lake Storage Gen1 jako HDFS kompatibilní úložiště. Pro tuto verzi pro clustery Hadoop a Storm ve Windows a Linuxu, můžete použít Data Lake Storage Gen1 pouze jako další úložiště. Tyto clustery dál používat Azure Storage (WASB) jako výchozího úložiště. Clustery HBase ve Windows a Linuxu, ale můžete Data Lake Storage Gen1 jako výchozí úložiště nebo další úložiště.

Pokyny o tom, jak zřídit cluster HDInsight s Data Lake Storage Gen1 najdete v tématu:

* [Zřídit cluster HDInsight s Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Zřízení clusteru služby HDInsight s Data Lake Storage Gen1 jako výchozí úložiště pomocí Azure Powershellu](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Zřízení clusteru služby HDInsight s Data Lake Storage Gen1 jako další úložiště pomocí Azure Powershellu](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Použití Data Lake Storage Gen1 s Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) umožňuje pracovat s velkými objemy dat v cloudovém měřítku. Dynamicky Zřizuje prostředky a umožňuje provádět analýzy terabajtů nebo dokonce exabajtů data ukládaná v počtu podporovaných zdrojů dat, jeden z nich se Gen1 úložiště Data Lake. Data Lake Analytics je speciálně optimalizovaná pro práci s Data Lake Storage Gen1 – nabízí tak nejvyšší úroveň výkonu, propustnosti a paralelizace pro úlohy s velkými objemy dat.

Pokyny týkající se použití Data Lake Analytics s Data Lake Storage Gen1 najdete v tématu [Začínáme s Data Lake Analytics pomocí Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Použití Data Lake Storage Gen1 s Azure Data Factory
Můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k ingestování dat z tabulky Azure, Azure SQL Database, datový sklad SQL Azure, objekty BLOB Azure Storage a místní databáze. Probíhá doma v ekosystému Azure, Azure Data Factory je možné provádět orchestraci příjmu dat z těchto zdroje do Data Lake Storage Gen1.

Pokyny k používání služby Azure Data Factory s Data Lake Storage Gen1 najdete v tématu [přesun dat do a z Data Lake Storage Gen1 pomocí služby Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1
Azure Data Lake Storage Gen1 poskytuje nástroj příkazového řádku, AdlCopy, který umožňuje kopírovat data z úložiště objektů Blob Azure do účtu Data Lake Storage Gen1. Další informace najdete v tématu [kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopírovat data mezi Azure SQL Database a Data Lake Storage Gen1
Použití Apache Sqoop k importu a exportu dat mezi Azure SQL Database a Data Lake Storage Gen1. Další informace najdete v tématu [kopírovat data mezi Data Lake Storage Gen1 a Azure SQL database pomocí Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Data Lake Storage Gen1 pomocí Stream Analytics
Data Lake Storage Gen1 jako jeden z výstupů slouží k ukládání dat, streamování, používat službu Azure Stream Analytics. Další informace najdete v tématu [Stream data z Azure Storage Blob do služby Data Lake Storage Gen1 pomocí Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Použití Data Lake Storage Gen1 s Power BI
Power BI můžete použít k importu dat z účtu Data Lake Storage Gen1 analyzovat a vizualizovat data. Další informace najdete v tématu [analýza dat v Data Lake Storage Gen1 pomocí Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Pomocí Data Lake Storage Gen1 s katalogem Data Catalog
Data z Data Lake Storage Gen1 můžete zaregistrovat do služby Azure Data Catalog zjistitelnost data v celé organizaci. Další informace najdete v části [data z Data Lake Storage Gen1 v registru ve službě Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Data Lake Storage Gen1 pomocí SQL Server Integration Services (SSIS)
Data Lake Storage Gen1 Správce připojení můžete použít v prostředí SSIS pro připojení k balíčku SSIS pomocí Data Lake Storage Gen1. Další informace najdete v tématu [použití Data Lake Storage Gen1 pomocí služby SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Pomocí Data Lake Storage Gen1 službou SQL Data Warehouse
Použijte PolyBase k načítání dat z Data Lake Storage Gen1 do SQL Data Warehouse. Další informace najdete v části [použití Data Lake Storage Gen1 službou SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Data Lake Storage Gen1 pomocí služby Azure Event Hubs
Azure Data Lake Storage Gen1 slouží k archivu a zachycení dat přijatých ze služby Azure Event Hubs. Další informace najdete v části [použití Data Lake Storage Gen1 pomocí Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme se službou Data Lake Storage Gen1 pomocí portálu](data-lake-store-get-started-portal.md)
* [Začínáme s Data Lake Storage Gen1 pomocí Powershellu](data-lake-store-get-started-powershell.md)  

