---
title: Nahrání velké datové sady do Azure Data Lake Storage Gen1 – offline metody
description: Použití služby Import a export ke kopírování dat z úložiště objektů blob Azure do služby Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839298"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Použití služby Import a export Azure pro offline kopii dat do data Lake Storage Gen1

V tomto článku se dozvíte, jak zkopírovat obrovské datové sady (>200 GB) do data Lake Storage Gen1 pomocí offline copy metod, jako je [služba Azure Import/Export](../storage/common/storage-import-export-service.md). Konkrétně soubor použitý jako příklad v tomto článku je 339,420,860,416 bajtů nebo přibližně 319 GB na disku. Nazvěme tento soubor 319GB.tsv.

Služba Import a export Azure vám pomůže bezpečněji přenášet velké objemy dat do úložiště objektů Blob Azure tak, že odesílá pevné disky do datového centra Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet úložiště Azure**.
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete [v tématu Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Příprava dat

Před použitím služby Import/Export rozdělte datový soubor, který má být přenesen, do kopií o velikosti **menší než 200 GB.** Nástroj pro import nefunguje se soubory většími než 200 GB. V tomto článku jsme rozdělit soubor do bloků 100 GB každý. Můžete to udělat pomocí [Cygwin](https://cygwin.com/install.html). Cygwin podporuje linuxové příkazy. V takovém případě použijte následující příkaz:

    split -b 100m 319GB.tsv

Operace rozdělení vytvoří soubory s následujícími názvy.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Příprava disků pomocí dat

Postupujte podle pokynů v [části Použití služby Import a export Azure](../storage/common/storage-import-export-service.md) (v části Příprava **disků)** k přípravě pevných disků. Zde je celková sekvence:

1. Obklijte pevný disk, který splňuje požadavek, který se má použít pro službu Import a export Azure.
2. Identifikujte účet úložiště Azure, kde se data zkopírují po odeslání do datového centra Azure.
3. Použijte [nástroj pro import a export Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), nástroj příkazového řádku. Tady je ukázkový úryvek, který ukazuje, jak nástroj používat.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Další ukázkové úryvky najdete [v tématu Použití služby Import a export Azure.](../storage/common/storage-import-export-service.md)
4. Předchozí příkaz vytvoří soubor deníku v zadaném umístění. Tento soubor deníku slouží k vytvoření úlohy importu z [portálu Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Vytvoření úlohy importu

Úlohu importu teď můžete vytvořit pomocí pokynů v [části Použití služby Import a export Azure](../storage/common/storage-import-export-service.md) (v části Vytvořit **úlohu importu).** Pro tuto úlohu importu, s dalšími podrobnostmi, také zadejte soubor deníku vytvořený při přípravě diskových jednotek.

## <a name="physically-ship-the-disks"></a>Fyzicky doručují disky

Teď můžete fyzicky doručovat disky do datového centra Azure. Tam se data zkopírují do objektů blobů Azure Storage, které jste zadali při vytváření úlohy importu. Také při vytváření úlohy, pokud jste se rozhodli poskytnout informace o sledování později, můžete se nyní vrátit k úloze importu a aktualizovat sledovací číslo.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Kopírování dat z objektů BLOB do úložiště datového jezera Gen1

Po stavu úlohy importu ukazuje, že je dokončena, můžete ověřit, zda jsou data k dispozici v objektech BLOB Azure Storage, které jste zadali. Potom můžete použít různé metody k přesunutí těchto dat z objektů BLOB do Azure Data Lake Storage Gen1. Všechny dostupné možnosti pro nahrávání dat najdete [v tématu Ingesting dat do data data Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

V této části vám poskytujeme definice JSON, které můžete použít k vytvoření kanálu Azure Data Factory pro kopírování dat. Tyto definice JSON můžete použít z [portálu Azure](../data-factory/tutorial-copy-data-portal.md) nebo [Visual Studia](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Zdrojová propojená služba (objekt blob úložiště Azure)

```JSON
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
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Cílová propojená služba (Data Lake Storage Gen1)

```JSON
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
```

### <a name="input-data-set"></a>Sada vstupních dat

```JSON
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
```

### <a name="output-data-set"></a>Výstupní sada dat

```JSON
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
```

### <a name="pipeline-copy-activity"></a>Potrubí (aktivita kopírování)

```JSON
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
```

Další informace najdete v tématu [Přesun dat z objektu blob úložiště Azure do Azure Data Lake Storage Gen1 pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Rekonstrukce datových souborů v úložišti datového jezera Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Začali jsme se souborem, který byl 319 GB, a rozdělili jsme ho na soubory menší velikosti, aby se daly přenést pomocí služby Azure Import/Export. Teď, když jsou data v Azure Data Lake Storage Gen1, můžeme rekonstruovat soubor na jeho původní velikost. Můžete k tomu použít následující rutiny Prostředí Azure PowerShell.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Další kroky

* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
