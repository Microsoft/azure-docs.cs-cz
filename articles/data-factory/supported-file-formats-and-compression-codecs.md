---
title: Podporované formáty souborů v Azure Data Factory
description: Toto téma popisuje formáty souborů a kompresní kódy, které jsou podporované konektory založené na souborech v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: dbcfad3dd3db9f5c9431e07d85d77a77a10283c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419030"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Podporované formáty souborů a kompresní kodeky v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Tento článek se týká následujících konektorů: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[Aktivitu Kopírovat](copy-activity-overview.md) můžete použít ke kopírování souborů tak, jak jsou mezi dvěma úložišti dat založených na souborech, v takovém případě jsou data zkopírována efektivně bez serializace nebo deserializace. 

Kromě toho můžete také analyzovat nebo generovat soubory daného formátu. Můžete například provést následující:

* Zkopírujte data z místní databáze SERVERU SQL Server a zapište do Azure Data Lake Storage Gen2 ve formátu Parket.
* Zkopírujte soubory v textovém formátu (CSV) z místního systému souborů a zapište do úložiště objektů Blob Azure ve formátu Avro.
* Zkopírujte soubory z webu z místního systému souborů, dekomprimujte je průběžně a zapisujte extrahované soubory do azure data lake storage gen2.
* Zkopírujte data ve formátu Gzip pro komprimovaný text (CSV) z úložiště objektů Blob Azure a zapište je do Azure SQL Database.
* Mnoho dalších aktivit, které vyžadují serializaci/deserializaci nebo kompresi/dekompresi.

## <a name="next-steps"></a>Další kroky

Podívejte se na další články aktivity kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)
