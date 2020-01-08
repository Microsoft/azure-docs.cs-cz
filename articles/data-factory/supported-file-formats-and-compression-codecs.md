---
title: Podporované formáty souborů v Azure Data Factory
description: Toto téma popisuje formáty souborů a komprese kódy, které jsou podporovány souborové konektorů v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439572"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Podporované formáty souborů a komprese kodeky ve službě Azure Data Factory

*Tento článek se týká následujících konektorů: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[Aktivitu kopírování](copy-activity-overview.md) můžete použít ke kopírování souborů mezi dvěma úložišti dat založených na souborech. v takovém případě se data zkopírují bez jakékoli serializace nebo deserializace. 

Kromě toho můžete také analyzovat nebo generovat soubory daného formátu. Například můžete provést následující:

* Kopírování dat z místní databáze SQL Server a zápis do Azure Data Lake Storage Gen2 ve formátu Parquet
* Kopírování souborů ve formátu textu (CSV) z místního systému souborů a zápis do úložiště objektů BLOB v Azure ve formátu Avro
* Zkopírujte soubory zip z místního systému souborů, dekomprimujte je průběžně a zapište extrahované soubory do Azure Data Lake Storage Gen2.
* Z úložiště objektů BLOB v Azure zkopírujte data ve formátu. komprimovaný text (CSV) gzip a napište ho do Azure SQL Database.
* Mnoho dalších aktivit, které vyžadují serializaci/deserializaci nebo kompresi/dekompresi.

## <a name="next-steps"></a>Další kroky

Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)
