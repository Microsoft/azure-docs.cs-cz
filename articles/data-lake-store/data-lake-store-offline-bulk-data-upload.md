---
title: Odeslat velkou datovou sadu do metod Azure Data Lake Storage Gen1-offline
description: Ke kopírování dat z úložiště objektů BLOB v Azure do Azure Data Lake Storage Gen1 použijte službu import/export.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d04a5c0e53e9a5db8bba03a5a9e9d95b87a8b5a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85855678"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Použijte službu import/export Azure pro offline kopírování dat a Data Lake Storage Gen1

V tomto článku se dozvíte, jak kopírovat obrovských datových sad (>200 GB) do Data Lake Storage Gen1 pomocí offline metod kopírování, jako je třeba [Služba import/export Azure](../storage/common/storage-import-export-service.md). Konkrétně soubor, který se používá jako příklad v tomto článku, je 339 420 860 416 bajtů nebo přibližně 319 GB na disku. Pojďme volat tento soubor 319GB. TSV.

Služba import/export Azure vám pomůže bezpečněji přenášet velké objemy dat do úložiště objektů BLOB v Azure prostřednictvím přenosu pevných disků do datacentra Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet služby Azure Storage**.
* **Účet Azure Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Příprava dat

Před použitím služby Import/export přerušit datový soubor, který se má přenést **do kopií, které mají velikost menší než 200 GB** . Nástroj pro import nefunguje se soubory většími než 200 GB. V tomto článku rozdělíme soubor na bloky 100 GB. To můžete provést pomocí [Cygwin](https://cygwin.com/install.html). Cygwin podporuje příkazy systému Linux. V takovém případě použijte následující příkaz:

```console
split -b 100m 319GB.tsv
```

Operace rozdělení vytvoří soubory s následujícími názvy.

* *319GB. TSV – část-AA*
* *319GB. TSV – část-AB*
* *319GB. TSV – část-AC*
* *319GB. TSV – část-AD*

## <a name="get-disks-ready-with-data"></a>Příprava disků na data

Podle pokynů v tématu věnovaném [použití služby Azure import/export](../storage/common/storage-import-export-service.md) (v části **Příprava jednotek** ) Připravte pevné disky. Zde je celková sekvence:

1. Pořízení pevného disku, který splňuje požadavky, které se mají použít pro službu Azure import/export.
2. Identifikujte účet služby Azure Storage, do kterého se budou data po odeslání do datacentra Azure kopírovat.
3. Použijte [Nástroj pro import/export Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), nástroj příkazového řádku. Zde je ukázkový fragment kódu, který ukazuje, jak nástroj použít.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Další ukázkové fragmenty najdete v tématu [použití služby Azure import/export](../storage/common/storage-import-export-service.md) .
4. Předchozí příkaz vytvoří soubor deníku v zadaném umístění. Pomocí tohoto souboru deníku můžete vytvořit úlohu importu z [Azure Portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Vytvoření úlohy importu

Nyní můžete vytvořit úlohu importu pomocí pokynů v tématu [použití služby Azure import/export](../storage/common/storage-import-export-service.md) (v části **Vytvoření úlohy importu** ). Pro tuto úlohu importu taky uveďte soubor deníku vytvořený při přípravě diskových jednotek.

## <a name="physically-ship-the-disks"></a>Fyzické dodávání disků

Disky teď můžete fyzicky dodávat do datového centra Azure. Tato data se zkopírují do objektů blob Azure Storage, které jste zadali při vytváření úlohy importu. Pokud se při vytváření úlohy rozhodnete poskytnout informace o sledování později, můžete se teď vrátit do úlohy importu a aktualizovat sledovací číslo.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Kopírování dat z objektů blob do Data Lake Storage Gen1

Jakmile se stav úlohy importu ukáže, že je dokončený, můžete ověřit, jestli jsou data dostupná v Azure Storagech objektech blob, které jste zadali. Pak můžete pomocí různých metod přesunout tato data z objektů blob do Azure Data Lake Storage Gen1. Všechny dostupné možnosti pro nahrávání dat najdete v tématu ingestování [dat do data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

V této části poskytujeme definice JSON, které můžete použít k vytvoření Azure Data Factoryho kanálu pro kopírování dat. Tyto definice JSON můžete použít z [Azure Portal](../data-factory/tutorial-copy-data-portal.md) nebo sady [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Zdrojová propojená služba (Azure Storage objekt BLOB)

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

### <a name="input-data-set"></a>Vstupní datová sada

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

### <a name="pipeline-copy-activity"></a>Kanál (aktivita kopírování)

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

Další informace najdete v tématu [přesun dat z Azure Storage objektů blob do Azure Data Lake Storage Gen1 pomocí Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Rekonstrukce datových souborů v Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zahájili jsme soubor, který byl 319 GB, a podařilo přerušit ho do souborů menší velikosti, aby bylo možné ho přenést pomocí služby pro import/export Azure. Teď, když jsou data v Azure Data Lake Storage Gen1, můžeme soubor znovu sestavit do původní velikosti. K tomu můžete použít následující rutiny Azure PowerShell.

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
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
