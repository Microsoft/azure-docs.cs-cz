---
title: Podporované formáty souborů podle aktivity kopírování v Azure Data Factory
description: Toto téma popisuje formáty souborů a kompresní kódy, které jsou podporovány aktivitou kopírování v Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364609"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Podporované formáty souborů a kompresní kodeky pomocí aktivity kopírování v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Tento článek se týká následujících konektorů: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[Aktivitu kopírování](copy-activity-overview.md) můžete použít ke kopírování souborů mezi dvěma úložišti dat založených na souborech. v takovém případě se data zkopírují bez jakékoli serializace nebo deserializace. 

Kromě toho můžete také analyzovat nebo generovat soubory daného formátu. Například můžete provést následující:

* Zkopírujte data z databáze SQL Server a zapište do Azure Data Lake Storage Gen2 ve formátu Parquet.
* Kopírování souborů ve formátu textu (CSV) z místního systému souborů a zápis do úložiště objektů BLOB v Azure ve formátu Avro
* Zkopírujte soubory zip z místního systému souborů, dekomprimujte je průběžně a zapište extrahované soubory do Azure Data Lake Storage Gen2.
* Z úložiště objektů BLOB v Azure zkopírujte data ve formátu. komprimovaný text (CSV) gzip a napište ho do Azure SQL Database.
* Mnoho dalších aktivit, které vyžadují serializaci/deserializaci nebo kompresi/dekompresi.

## <a name="next-steps"></a>Další kroky

Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)
