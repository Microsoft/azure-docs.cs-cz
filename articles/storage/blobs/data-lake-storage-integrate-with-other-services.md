---
title: Integrace s dalšími službami Azure, Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Vysvětlení, jak Azure Data Lake Storage Gen2 integruje s dalšími službami Azure
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885656"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Integrace s dalšími službami Azure, Azure Data Lake Storage Gen2

Služby Azure můžete použít k ingestování, analýza a vizualizace dat ve vašem účtu úložiště Azure Data Lake Storage Gen2. Vyberte si služby, které nejlíp vyhovují této úlohy, které se pokoušíte provést.

## <a name="ingest-data-into-your-data-lake"></a>Ingestovat data do vaší data lake

Tyto služby můžete naplnit vaše data lake s daty.

### <a name="azure-data-factory"></a>Azure Data Factory

Můžete použít [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) k ingestování dat z těchto zdrojů:

* Tabulky Azure
* Azure SQL Database
* Azure SQL DataWarehouse
* Azure Storage Blobs
* Místní databáze

Zobrazit [přesun dat do a z Data Lake Storage Gen2 pomocí služby Data Factory](../../data-factory/connector-azure-data-lake-store.md).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Analýza a vizualizace dat ve službě data lake

Tyto služby můžete použít Data Lake Storage Gen2 jako koncový bod úložiště.

### <a name="azure-hdinsight"></a>Azure HDInsight

Můžete zřídit [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) clusteru, který používá jako HDFS kompatibilní úložiště Data Lake Storage Gen2. Pro tuto verzi pro clustery Hadoop a Storm ve Windows a Linuxu, můžete použít Data Lake Storage Gen2 pouze jako další úložiště. Tyto clustery dál používat Azure Storage (WASB) jako výchozího úložiště. Ale pro clustery HBase ve Windows a Linuxu, můžete použít Data Lake Storage Gen2 jako výchozím úložištěm a jako další úložiště.

Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Můžete použít [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) pro práci s velkými objemy dat v cloudovém měřítku. Dynamicky Zřizuje prostředky a umožňuje analyzovat exabajtech dat. Data Lake Analytics je optimalizovaná pro použití Data Lake Storage Gen2 jako zdroj dat. 

Zobrazit [Začínáme s Data Lake Analytics pomocí Data Lake Storage Gen2](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="copy-data-to-other-repositories"></a>Kopírování dat do jiných úložišť

Tyto služby využijte k kopírování dat z vašich data lake a umístí je do jiných úložišť.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Použijte PolyBase k načítání dat z Data Lake Storage Gen2 do SQL Data Warehouse. Další informace najdete v části [použití Data Lake Storage Gen2 službou SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [Přehled služby Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Pro potřeby velkého objemu dat pomocí Azure Data Lake Storage Gen2](data-lake-storage-data-scenarios.md)
