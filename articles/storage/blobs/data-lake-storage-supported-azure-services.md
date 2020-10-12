---
title: Služby Azure, které podporují Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informace o tom, které služby Azure se integrují s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: c3a5f3a984c95af400c9e0c1543e3c1883290668
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442952"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Služby Azure, které podporují Azure Data Lake Storage Gen2

Služby Azure můžete použít k ingestování dat, provádění analýz a vytváření vizuálních reprezentace. Tento článek obsahuje seznam podporovaných služeb Azure, zveřejňuje jejich úroveň podpory a poskytuje odkazy na články, které vám pomůžou tyto služby používat s Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Podporované služby Azure

V této tabulce jsou uvedené služby Azure, které můžete používat s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách, se v průběhu času změní, dokud je podpora stále rozbalená.

> [!NOTE]
> Úroveň podpory odkazuje jenom na to, jak je služba podporovaná s Data Lake Storage Gen 2.

|Služba Azure |Úroveň podpory |Azure AD |Sdílený klíč| Související články |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Obecná dostupnost|Yes|Yes|[Načtení dat do Azure Data Lake Storage Gen2 s využitím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Obecná dostupnost|Yes|Yes|[Použití se službou Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Rychlý Start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Kurz: přístup k datům Data Lake Storage Gen2 pomocí Azure Databricks pomocí Sparku](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Obecná dostupnost|No|Yes|[Zachycení událostí prostřednictvím Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Obecná dostupnost|Yes|Yes|[Kurz: implementace vzoru Data Lake Capture pro aktualizaci rozdílové tabulky datacihly](data-lake-storage-events.md)|
|Azure Logic Apps|Obecná dostupnost|No|Yes|[Přehled – co je Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Obecná dostupnost|Yes|Yes|[Přístup k datům ve službě Azure Storage](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Obecná dostupnost|Yes|Yes|[Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Odchozí přenos dat do Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Obecná dostupnost|No|Yes|[Pomocí Azure Data Box migrujte data z místního úložiště HDFS do Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Obecná dostupnost|Yes|Yes|[Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Použití rozhraní příkazového řádku HDFS s Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Kurz: extrakce, transformace a načtení dat pomocí Apache Hive ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Obecná dostupnost|No|Yes|[Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Obecná dostupnost|Yes|Yes|[Analyzovat data v Data Lake Storage Gen2 pomocí Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (dříve SQL Data Warehouse)|Obecná dostupnost|Yes|Yes|[Použití s Azure synapse Analytics (dříve SQL Data Warehouse)](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|Služba SSIS (SQL Server Integration Services)|Obecná dostupnost|Yes|Yes|[Správce připojení Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Průzkumník dat Azure|Obecná dostupnost|Yes|Yes|[Dotazování dat v Azure Data Lake pomocí Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Preview|Yes|Yes|[Index a hledání Azure Data Lake Storage Gen2ch dokumentů (Preview)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Content Delivery Network|Zatím nepodporováno|Nelze použít|Nelze použít|[Index a hledání Azure Data Lake Storage Gen2ch dokumentů (Preview)](https://docs.microsoft.com/azure/cdn/cdn-overview)|

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)