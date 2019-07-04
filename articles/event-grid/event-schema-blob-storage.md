---
title: Azure Event Grid objektu blob úložiště událostí schématu
description: Popisuje vlastnosti, které jsou k dispozici pro události služby blob storage pomocí služby Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445767"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schéma událostí Azure Event Grid pro úložiště objektů Blob

Tento článek obsahuje vlastnosti a schéma pro události služby blob storage. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj události úložiště](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Seznam událostí pro rozhraní API REST služby Blob

Tyto události se zobrazí, když klient vytvoří, nahradí nebo odstraní objekt blob pomocí volání rozhraní REST API služby Blob.

 |Název události |Popis|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Aktivováno, když se vytvoří nebo nahradí objekt blob. <br>Konkrétně tato událost se aktivuje, když klienti používají `PutBlob`, `PutBlockList`, nebo `CopyBlob` operace, které jsou k dispozici v rozhraní REST API objektů Blob.   |
 |**Microsoft.Storage.BlobDeleted** |Aktivováno, když se odstraní objekt blob. <br>Konkrétně tato událost se aktivuje při volání klienta `DeleteBlob` operace, která je k dispozici v rozhraní REST API objektů Blob. |

> [!NOTE]
> Pokud chcete zajistit, aby **Microsoft.Storage.BlobCreated** událost se aktivuje, jenom když je objekt Blob bloku zcela potvrzeny, filtrovat události pro `CopyBlob`, `PutBlob`, a `PutBlockList` volání rozhraní REST API. Tato aktivační událost volání rozhraní API **Microsoft.Storage.BlobCreated** událostí až po data jsou zcela potvrzeny do objektu Blob bloku. Zjistěte, jak vytvořit filtr, najdete v článku [pro Event Grid umožňuje filtrovat události](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Seznam událostí pro Azure Data Lake Storage Gen 2 rozhraní REST API

Tyto události se aktivuje, pokud povolíte hierarchického oboru názvů v účtu úložiště a klienti volání REST API služby Azure Data Lake Storage Gen2.

> [!NOTE]
> Tyto události jsou ve verzi public preview a jsou k dispozici pouze **USA – západ 2** a **střed USA – západ** oblastech.

 |Název události|Popis|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Aktivováno, když se vytvoří nebo nahradí objekt blob. <br>Konkrétně tato událost se aktivuje, když klienti používají `CreateFile` a `FlushWithClose` operace, které jsou k dispozici v Azure Data Lake Storage Gen2 rozhraní REST API. |
 |**Microsoft.Storage.BlobDeleted** |Aktivováno, když se odstraní objekt blob. <br>Konkrétně tato událost se také aktivuje při volání klienta `DeleteFile` operace, která je k dispozici v Azure Data Lake Storage Gen2 rozhraní REST API. |
 |**Microsoft.Storage.BlobRenamed**|Aktivováno, když je přejmenován objektu blob. <br>Konkrétně tato událost se aktivuje, když klienti používají `RenameFile` operace, která je k dispozici v Azure Data Lake Storage Gen2 rozhraní REST API.|
 |**Microsoft.Storage.DirectoryCreated**|Aktivováno, když je vytvořen adresář. <br>Konkrétně tato událost se aktivuje, když klienti používají `CreateDirectory` operace, která je k dispozici v Azure Data Lake Storage Gen2 rozhraní REST API.|
 |**Microsoft.Storage.DirectoryRenamed**|Aktivovat při přejmenování adresáře. <br>Konkrétně tato událost se aktivuje, když klienti používají `RenameDirectory` operace, která je k dispozici v Azure Data Lake Storage Gen2 rozhraní REST API.|
 |**Microsoft.Storage.DirectoryDeleted**|Aktivováno, když se odstraní adresář. <br>Konkrétně tato událost se aktivuje, když klienti používají `DeleteDirectory` operace, která je k dispozici v Azure Data Lake Storage Gen2 rozhraní REST API.|

> [!NOTE]
> Pokud chcete zajistit, aby **Microsoft.Storage.BlobCreated** událost se aktivuje, jenom když je objekt Blob bloku zcela potvrzeny, filtrovat události pro `FlushWithClose` volání rozhraní REST API. Toto rozhraní API volat aktivační události **Microsoft.Storage.BlobCreated** událostí až po data jsou zcela potvrzeny do objektu Blob bloku. Zjistěte, jak vytvořit filtr, najdete v článku [pro Event Grid umožňuje filtrovat události](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Obsah při reakci na události

Když se aktivuje událost služby Event Grid odesílá data o této události odběratelská koncového bodu.

Tato část obsahuje příklad těchto dat by vypadat pro každou jednotlivou událost úložiště objektů blob.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated události

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Událost Microsoft.Storage.BlobCreated (Data Lake Storage Gen2)

Pokud je účet blob storage hierarchického oboru názvů, data vypadá podobně jako předchozí příklad s výjimkou tyto změny:

* `dataVersion` Klíč nastavený na hodnotu `2`.

* `data.api` Klíč nastavený na řetězec `CreateFile` nebo `FlushWithClose`.

* `contentOffset` Klíč je zahrnutý v datové sadě.

> [!NOTE]
> Pokud se aplikace používají `PutBlockList` operace nahrát nový objekt blob do účtu data nesmí obsahovat tyto změny.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted události

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Událost Microsoft.Storage.BlobDeleted (Data Lake Storage Gen2)

Pokud je účet blob storage hierarchického oboru názvů, data vypadá podobně jako předchozí příklad s výjimkou tyto změny:

* `dataVersion` Klíč nastavený na hodnotu `2`.

* `data.api` Klíč nastavený na řetězec `DeleteFile`.

* `url` Klíč obsahuje cestu `dfs.core.windows.net`.

> [!NOTE]
> Pokud se aplikace používají `DeleteBlob` operace odstranění objektu blob z účtu, data nesmí obsahovat tyto změny.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed události

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated události

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed události

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted události

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| topic | string | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| subject | string | Vydavatel definované cesta předmět události. |
| eventType | string | Jeden z typů registrované události pro tento zdroj událostí. |
| eventTime | string | Vygenerování události podle času UTC poskytovatele. |
| id | string | Jedinečný identifikátor pro událost. |
| data | objekt | Data události úložiště objektů BLOB. |
| dataVersion | string | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| metadataVersion | string | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| api | string | Operace, který spustil danou událost. |
| clientRequestId | string | id požadavku poskytované klienta pro ukládání operace rozhraní API. Toto id lze použít ke korelaci diagnostické protokoly služby Azure Storage pomocí pole "client-request-id" v protokolech a lze zadat pomocí "x-ms klienta request-id" záhlaví žádosti klientů. Zobrazit [formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Id generovaných službou požadavku pro operaci úložiště rozhraní API. Můžete použít ke korelaci do služby Azure Storage diagnostické protokoly pomocí pole "hlavička požadavku id" v protokolech a vrátí se v inicializaci volání rozhraní API v hlavičce "x-ms-request-id". Zobrazit [formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | Hodnota, která můžete použít k provádění operací podmíněně. |
| contentType | string | Pro tento objekt blob zadaný typ obsahu. |
| contentLength | integer | Velikost objektu blob v bajtech. |
| blobType | string | Typ objektu blob. Platné hodnoty jsou "BlockBlob" nebo "PageBlob". |
| parametr ContentOffset rovný | číslo | Posun v bajtech operace zápisu provedených v okamžiku, kdy aplikace aktivuje událost dokončila zápis do souboru. <br>Zobrazí se pouze pro události aktivované na účty úložiště blob, které mají hierarchického oboru názvů.|
| destinationUrl |string | Adresa url souboru, který bude existovat po dokončení operace. Například, pokud je soubor přejmenován `destinationUrl` vlastnost obsahuje adresu url nového názvu souboru. <br>Zobrazí se pouze pro události aktivované na účty úložiště blob, které mají hierarchického oboru názvů.|
| sourceUrl |string | Adresa url souboru, který existuje před operaci. Například, pokud je soubor přejmenován `sourceUrl` obsahuje adresu url původní název souboru před operace přejmenování. <br>Zobrazí se pouze pro události aktivované na účty úložiště blob, které mají hierarchického oboru názvů. |
| url | string | Cesta k objektu blob. <br>Pokud klient používá rozhraní REST API objektů Blob, je adresa url má tuto strukturu:  *\<název účtu úložiště\>.blob.core.windows.net/\<název kontejneru\>/\<název souboru \>* . <br>Pokud klient používá rozhraní REST API Data Lake Storage, je adresa url má tuto strukturu:  *\<název účtu úložiště\>.dfs.core.windows.net/\<název systému souborů\> / \<název souboru\>* .
|
| recursive| string| `True` k provedení této operace na všechny podřízené adresářů. v opačném případě `False`. <br>Zobrazí se pouze pro události aktivované na účty úložiště blob, které mají hierarchického oboru názvů. |
| sequencer | string | Hodnota typu neprůhledný řetězec představující logickou posloupnost událostí pro jakýkoli název konkrétního objektu blob.  Uživatele můžete použít standardní porovnání řetězců k pochopení relativního pořadí dvou událostí na stejný název objektu blob. |
| storageDiagnostics | objekt | Diagnostická data v některých součástí služby Azure Storage. Pokud je k dispozici, by měl být ignorován příjemci událostí. |

|Vlastnost|Typ|Popis|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostí služby blob storage, najdete v článku [trasy objektu Blob úložiště události – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
