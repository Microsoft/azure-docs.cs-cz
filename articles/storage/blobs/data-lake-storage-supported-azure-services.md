---
title: Služby Azure, které podporují Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft
description: Informace o integraci služeb Azure s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878319"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Služby Azure, které podporují Azure Data Lake Storage Gen2

Služby Azure můžete používat k ingestování dat, provádění analýz a vytváření vizuálních reprezentací. Tento článek obsahuje seznam podporovaných služeb Azure, zveřejňuje jejich úroveň podpory a poskytuje odkazy na články, které vám pomohou používat tyto služby s Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Podporované služby Azure

V této tabulce jsou uvedeny služby Azure, které můžete používat s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách se bude měnit v průběhu času jako podpora nadále rozšiřovat.

> [!NOTE]
> Úroveň podpory odkazuje pouze na to, jak je služba podporována pomocí data lake storage gen 2.

|Služba Azure |Úroveň podpory |Související články |
|---------------|-------------------|---|
|Azure Data Factory|Obecná dostupnost|[Načtení dat do Azure Data Lake Storage Gen2 s Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Obecná dostupnost|[Použití se službou Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Úvodní příručka: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Kurz: Extrahujte, transformujte a načtěte data pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Kurz: Přístup k datům Gen2 úložiště datového jezera pomocí Azure Databricks pomocí Sparku](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Obecná dostupnost|[Zachyťte události prostřednictvím Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Obecná dostupnost|[Kurz: Implementace vzoru sběru datového jezera k aktualizaci tabulky Databricks Delta](data-lake-storage-events.md)|
|Azure Logic Apps|Obecná dostupnost|[Přehled – Co jsou aplikace Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Obecná dostupnost|[Přístup k datům ve službách úložiště Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Obecná dostupnost|[Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Výstup do Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Obecná dostupnost|[Migrace dat z místního úložiště HDFS do Úložiště Azure se používá k migraci dat z místního úložiště HDFS](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Obecná dostupnost|[Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Použití rozhraní SE konstruum HDFS s datovým úložištěm jezera Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Kurz: Extrahujte, transformujte a načítajíte data pomocí Apache Hive na Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Obecná dostupnost|[Směrování zpráv služby IoT Hub slouží k odesílání zpráv mezi zařízeními a cloudy do různých koncových bodů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Obecná dostupnost|[Analýza dat v zařízení Data Lake Storage Gen2 pomocí Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Obecná dostupnost|[Použití se službou Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Obecná dostupnost|[Správce připojení azure úložiště](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Preview|[Indexování a vyhledávání dokumentů Azure Data Lake Storage Gen2 (preview)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Průzkumník dat Azure|Preview|[Dotazování na data v Azure Data Lake pomocí Azure Data Exploreru](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Dosud není podporováno|[Indexování a vyhledávání dokumentů Azure Data Lake Storage Gen2 (preview)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům v úložišti Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)