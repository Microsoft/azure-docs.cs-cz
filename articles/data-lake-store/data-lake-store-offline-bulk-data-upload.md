---
title: Nahrát velké objemy dat do Data Lake Store pomocí offline metod | Microsoft Docs
description: Použijte nástroj AdlCopy ke zkopírování dat z Azure úložiště objektů BLOB do Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 03d8cc4440d7adcb947e7c150a565e51b6401e92
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Používat službu Azure Import/Export pro offline kopii dat do Data Lake Store
V tomto článku se dozvíte kopírování obrovských sad dat (> 200 GB) do Azure Data Lake Store pomocí metod kopii offline, jako je třeba [služba Azure Import/Export](../storage/common/storage-import-export-service.md). Soubor používá jako příklad v tomto článku je konkrétně 339,420,860,416 bajtů nebo přibližně 319 GB na disku. Umožňuje volání 319GB.tsv tento soubor.

Služba Azure Import/Export vám umožní bezpečněji přenos velkých objemů dat do úložiště objektů Blob v Azure pomocí přesouvání pevných disků o datovém centru Azure.

## <a name="prerequisites"></a>Požadavky
Než začnete, musíte mít následující:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure**.
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Příprava dat

Než začnete používat službu Import/Export, rozdělit datového souboru k přesunu **do kopie, které jsou menší než 200 GB** velikost. Nástroj pro import nefunguje s soubory větší než 200 GB. V tomto kurzu jsme rozdělit do bloků, 100 GB. Můžete to provést pomocí [emulaci](https://cygwin.com/install.html). Emulaci podporuje příkazy Linux. V takovém případě použijte následující příkaz:

    split -b 100m 319GB.tsv

Operace rozdělení vytvoří soubory s následujícími názvy.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Příprava disky s daty
Postupujte podle pokynů v [pomocí služby Azure Import/Export](../storage/common/storage-import-export-service.md) (v části **Příprava jednotky** část) k přípravě pevné disky. Tady je celkový pořadí:

1. Pořídit pevný disk, který splňuje požadavek na který se má použít pro službu Azure Import/Export.
2. Určete účet úložiště Azure, kde data se zkopírují po je dodáván na datovém centru Azure.
3. Použití [nástroj Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), nástroj příkazového řádku. Zde je ukázka fragment kódu, který ukazuje, jak používat nástroj.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    V tématu [pomocí služby Azure Import/Export](../storage/common/storage-import-export-service.md) pro další fragmenty ukázka.
4. Předchozí příkaz vytvoří soubor deníku do zadaného umístění. Tento soubor deníku použít k vytvoření úlohy importu z [portál Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Vytvoření úlohy importu
Nyní můžete vytvořit úlohy importu pomocí pokynů uvedených v [pomocí služby Azure Import/Export](../storage/common/storage-import-export-service.md) (v části **vytvořit úlohy importu** části). Pro tuto úlohu importu s další podrobnosti, také poskytněte soubor deníku vytvořili při přípravě na pevných discích.

## <a name="physically-ship-the-disks"></a>Fyzicky dodávat disky
Nyní můžete fyzicky dodávat disky datového centra Azure. Existuje ale data se zkopírují přes Azure úložiště objektů BLOB, které jste zadali při vytváření úlohy importu. Navíc při vytváření úlohy, když jste se rozhodli poskytnout informace o sledování později, můžete nyní přejděte zpět na vaše úlohy importu a aktualizovat číslo sledování.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Kopírování dat z objektů BLOB služby Azure Storage do Azure Data Lake Store
Po ve stavu úlohy importu zobrazuje, zda je dokončena, můžete ověřit, zda je k dispozici do objektů BLOB Azure Storage, měl zadaná data. Potom můžete různé metody pro přesun dat z objektů BLOB do Azure Data Lake Store. Všechny dostupné možnosti, pro nahrávání dat najdete v tématu [příjem dat do Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

V této části nám umožňují JSON definice, které můžete použít k vytvoření kanál služby Azure Data Factory pro kopírování dat. Můžete použít tyto definice JSON z [portál Azure](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), nebo [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Zdroj propojené služby (objekt blob úložiště Azure)
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Cíl propojené služby (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Vstupní datové sady
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Výstupní datové sady
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Kanál (aktivita kopírování)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Další informace najdete v tématu [přesun dat z objektu blob úložiště Azure do Azure Data Lake Store pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Rekonstrukci datových souborů v Azure Data Lake Store
Spuštění se souborem, který byl 319 GB a překročila ho dolů do souborů menší velikost tak, že by mohla být přenesena pomocí služby Azure Import/Export. Teď, když se data v Azure Data Lake Store, jsme rekonstrukci soubor původní velikost. K tomu můžete použít následující cmldts prostředí Azure PowerShell.

````
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext -SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
````

## <a name="next-steps"></a>Další postup
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
