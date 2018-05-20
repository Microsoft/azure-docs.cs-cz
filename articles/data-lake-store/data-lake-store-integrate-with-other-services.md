---
title: Integrace s jinými službami Azure Data Lake Store | Microsoft Docs
description: Pochopit, jak Data Lake Store se integruje s jinými službami Azure
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e11100118f599b4fee6362625aff05c50bb07c87
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrace Data Lake Store s ostatními službami Azure
Azure Data Lake Store můžete použít ve spojení s jinými službami Azure umožňuje širší škálu scénářů. V následujícím článku jsou uvedené služby, které se dá integrovat Data Lake Store.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Použití Data Lake Store s Azure HDInsight
Můžete zřídit [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) clusteru, který používá jako HDFS kompatibilní úložiště Data Lake Store. Pro tuto verzi pro clustery Hadoop a Storm v systému Windows a Linux, můžete Data Lake Store pouze jako další úložiště. Tyto clustery dál používat jako výchozí úložiště Azure Storage (WASB). Clustery HBase v systému Windows a Linux, ale můžete Data Lake Store jako výchozí úložiště nebo další úložiště.

Pokyny o tom, jak zřídit cluster služby HDInsight s Data Lake Store najdete v tématu:

* [Zřízení clusteru HDInsight s Data Lake Store pomocí portálu Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Zřízení clusteru HDInsight s Data Lake Store jako výchozí úložiště pomocí prostředí Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Zřízení clusteru HDInsight s Data Lake Store jako další úložiště pomocí prostředí Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Použití Data Lake Store s Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) umožňuje pracovat s velkým objemem dat v cloudovém měřítku. Dynamicky Zřizuje prostředky a umožňuje provádět analýzy terabajtů nebo dokonce exabajtů dat., které můžou být uložené v počtu podporovaných zdrojů dat, jeden z nich se Data Lake Store. Data Lake Analytics je speciálně optimalizovaná pro práci s Azure Data Lake Store – poskytuje nejvyšší úroveň výkonu, propustnosti a paralelizace pro úlohy big data.

Pokyny o tom, jak používat Data Lake Analytics s Data Lake Store najdete v tématu [Začínáme s Data Lake Analytics pomocí Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Použití Data Lake Store pomocí Azure Data Factory.
Můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pro načítání dat z tabulky Azure, Azure SQL Database, datový sklad SQL Azure, objektů BLOB služby Azure Storage a místní databáze. S občanstvím první třídy v ekosystému Azure, Azure Data Factory slouží k orchestraci přijímání dat z těchto zdroje do Azure Data Lake Store.

Pokyny o tom, jak používat Azure Data Factory s Data Lake Store najdete v tématu [přesun dat do a z Data Lake Store pomocí služby Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Ke zkopírování dat z úložiště objektů BLOB Azure do Data Lake Store
Azure Data Lake Store poskytuje nástroj příkazového řádku, AdlCopy, která umožňuje kopírování dat z Azure Blob Storage do účtu Data Lake Store. Další informace najdete v tématu [kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Kopírovat data mezi Azure SQL Database a Data Lake Store
Apache Sqoop slouží k importu a exportu dat mezi Azure SQL Database a Data Lake Store. Další informace najdete v tématu [kopírovat data mezi Data Lake Store a Azure SQL database pomocí Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Použití Data Lake Store pomocí služby Stream Analytics
Data Lake Store jako jeden z výstupů slouží k ukládání dat streamování pomocí služby Azure Stream Analytics. Další informace najdete v tématu [Streamovat data z Azure Storage Blob do Data Lake Store pomocí Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Použití Data Lake Store s Power BI
Power BI můžete importovat data z účtu Data Lake Store k analýze a vizualizovat data. Další informace najdete v tématu [v Data Lake Store pomocí Power BI analyzovat data](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Použití Data Lake Store pomocí katalogu Data Catalog
Data z Data Lake Store můžete zaregistrovat do Azure Data Catalog zjistitelnost data v rámci organizace. Další informace najdete v části [zaregistrovat v Azure Data Catalog dat z Data Lake Store](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Použití Data Lake Store s SQL Server Integration Services (služby SSIS)
Správce připojení Azure Data Lake Store v SSIS slouží k připojení balíčku služby SSIS s Azure Data Lake Store. Další informace najdete v tématu [použití Data Lake Store s SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Použití Data Lake Store s SQL Data Warehouse
PolyBase můžete použít k načtení dat z Azure Data Lake Store do SQL Data Warehouse. Další informace najdete v části [použití Data Lake Store s SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Použití Data Lake Store s Azure Event Hubs
Azure Data Lake Store můžete archivu a zachycení dat přijatých Azure Event Hubs. Další informace najdete v části [použití Data Lake Store s Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Store](data-lake-store-overview.md)
* [Začínáme s Data Lake Store pomocí portálu](data-lake-store-get-started-portal.md)
* [Začínáme s Data Lake Store pomocí prostředí PowerShell](data-lake-store-get-started-powershell.md)  

