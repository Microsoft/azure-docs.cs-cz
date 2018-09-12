---
title: Nahrávání velkých objemů dat do Azure Data Lake Storage Gen1 pomocí offline metody | Dokumentace Microsoftu
description: Použijte nástroj AdlCopy ke kopírování dat z Azure Storage BLOB do Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 6430bf524ac81af242bf7afb4c2c8196309806ab
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391671"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Pomocí služby Azure Import/Export pro offline kopírování dat do Azure Data Lake Storage Gen1
V tomto článku se dozvíte víc o kopírování obrovských sad dat (> 200 GB) do Azure Data Lake Storage Gen1 pomocí offline kopírování metod, jako je třeba [služba Azure Import/Export](../storage/common/storage-import-export-service.md). Soubor použitý jako příklad v tomto článku je konkrétně 339,420,860,416 bajtů nebo přibližně 319 GB na disku. Pojmenujme tuto 319GB.tsv souboru.

Služba Azure Import/Export vám umožní bezpečněji přenášet velké objemy dat do Azure Blob storage přenosem pevných disků do datacentra Azure.

## <a name="prerequisites"></a>Požadavky
Než začnete, musíte mít následující:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure**.
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Příprava dat

Než začnete používat službu Import/Export, přerušit datový soubor, které se mají přenést **do kopie, které jsou kratší než 200 GB** velikosti. Nástroj pro import nefunguje s soubory větší než 200 GB. V tomto kurzu rozdělíme tento soubor do bloků dat s 100 GB. Můžete to provést pomocí [Cygwin](https://cygwin.com/install.html). Cygwin podporuje Linuxové příkazy. V takovém případě použijte následující příkaz:

    split -b 100m 319GB.tsv

Operace rozdělení vytvoří soubory s následujícími názvy.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Příprava disky s daty
Postupujte podle pokynů v [pomocí služby Azure Import/Export](../storage/common/storage-import-export-service.md) (v části **Příprava jednotky** části) pro přípravu pevných disků. Toto je celkový pořadí:

1. Pořídit pevný disk, který splňuje požadavky pro službu Azure Import/Export.
2. Identifikujte účet úložiště Azure, ve kterém data se zkopírují po dodává se do datacentra Azure.
3. Použití [nástrojem Import/Export Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), nástroje příkazového řádku. Tady je ukázka fragment kódu, který ukazuje, jak používat nástroj.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Zobrazit [pomocí služby Azure Import/Export](../storage/common/storage-import-export-service.md) pro víc fragmentů kódu ukázky.
4. Předchozí příkaz vytvoří soubor deníku v zadaném umístění. Tento soubor deníku použít k vytvoření úlohy importu z [webu Azure portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Vytvořit úlohu importu
Nyní můžete vytvořit úlohu importu pomocí pokynů v [pomocí služby Azure Import/Export](../storage/common/storage-import-export-service.md) (v části **vytvořit úlohu importu** části). Pro tuto úlohu importu se další podrobnosti najdete také poskytnout soubor deníku vytvořili při přípravě na pevných discích.

## <a name="physically-ship-the-disks"></a>Fyzicky zasílejte disky
Nyní můžete fyzicky zasílejte disků do datacentra Azure. Data se tam zkopíruje do objektů BLOB Azure Storage, který jste zadali při vytváření úlohy importu. Navíc při vytváření úlohy, když jste se rozhodli zadání informací o sledování později, můžete nyní přejít zpět do úlohy importu a aktualizovat číslo pro sledování.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopírování dat z Azure Storage BLOB do Azure Data Lake Storage Gen1
Po jeho dokončení zobrazuje stav úlohy importu, můžete ověřit, zda je k dispozici v objektech BLOB Azure Storage, měl zadaná data. Potom můžete celé řady metod pro přesun dat z objektů BLOB do Azure Data Lake Storage Gen1. Všechny dostupné možnosti pro nahrávání dat, naleznete v tématu [Ingestovat data do Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

V této části vám poskytneme o definice JSON, které můžete použít k vytvoření kanálu Azure Data Factory pro kopírování dat. Můžete použít tyto definice JSON z [webu Azure portal](../data-factory/tutorial-copy-data-portal.md) nebo [sady Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

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

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Cílit na propojenou službu (Azure Data Lake Storage Gen1)
````
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
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
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
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
Další informace najdete v tématu [přesun dat z Azure Storage blob do služby Azure Data Lake Storage Gen1 pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Obnovit data souborů v Azure Data Lake Storage Gen1
Začali jsme se souborem, který byl 319 GB a to vypneme dostalo do souborů menší velikost tak, aby mohl být přenosu pomocí služby Azure Import/Export. Teď, když data v Azure Data Lake Storage Gen1, jsme obnovit původní velikost souboru. K tomu slouží následující rutiny prostředí Azure PowerShell.

```
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext -SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
````

## <a name="next-steps"></a>Další postup
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
