---
title: Azure Event Grid schéma událostí úložiště objektů BLOB
description: Popisuje vlastnosti, které jsou k dispozici pro události služby Blob Storage s Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: fa638a00e0d35e1d48bc3205ce2a77e7faf5d22e
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718385"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid schéma událostí pro úložiště objektů BLOB

Tento článek poskytuje vlastnosti a schéma pro události služby Blob Storage. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

Seznam ukázkových skriptů a kurzů najdete v tématu [zdroj události úložiště](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Seznam událostí pro rozhraní REST API objektů BLOB

Tyto události se aktivují, když klient vytvoří, nahradí nebo odstraní objekt BLOB voláním rozhraní REST API pro objekty blob.

 |Název události |Popis|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Aktivováno, když je objekt BLOB vytvořen nebo nahrazen. <br>Konkrétně se tato událost aktivuje, když klienti použijí operace `PutBlob`, `PutBlockList` nebo `CopyBlob`, které jsou k dispozici v REST API objektů BLOB.   |
 |**Microsoft. Storage. BlobDeleted** |Aktivuje se při odstranění objektu BLOB. <br>Konkrétně se tato událost aktivuje, když klienti volají operaci `DeleteBlob`, která je k dispozici v REST API objektů BLOB. |

> [!NOTE]
> Chcete-li zajistit, aby se událost **Microsoft. Storage. BlobCreated** aktivovala pouze v případě, že je objekt blob bloku zcela potvrzen, vyfiltrujte událost pro REST API volání `CopyBlob`, `PutBlob` a `PutBlockList`. Tato volání rozhraní API aktivují událost **Microsoft. Storage. BlobCreated** až po úplném potvrzení dat do objektu blob bloku. Další informace o tom, jak vytvořit filtr, najdete v tématu [filtrování událostí pro Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Seznam událostí pro rozhraní REST API pro Azure Data Lake Storage Gen 2

Tyto události se aktivují, pokud povolíte hierarchický obor názvů v účtu úložiště a klienti volají Azure Data Lake Storage Gen2 rozhraní REST API.

> [!NOTE]
> Tyto události jsou ve verzi Public Preview a jsou dostupné jenom pro oblasti **západní USA 2** a **středozápadní USA** .

 |Název události|Popis|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** | Aktivováno, když je objekt BLOB vytvořen nebo nahrazen. <br>Konkrétně se tato událost aktivuje, když klienti použijí operace `CreateFile` a `FlushWithClose`, které jsou k dispozici v REST API Azure Data Lake Storage Gen2. |
 |**Microsoft. Storage. BlobDeleted** |Aktivuje se při odstranění objektu BLOB. <br>Konkrétně se tato událost aktivuje také v případě, že klienti volají operaci `DeleteFile`, která je k dispozici ve Azure Data Lake Storage Gen2 REST API. |
 |**Microsoft. Storage. BlobRenamed**|Aktivuje se při přejmenování objektu BLOB. <br>Konkrétně se tato událost aktivuje, když klienti použijí operaci `RenameFile`, která je k dispozici ve Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft. Storage. DirectoryCreated**|Aktivuje se při vytvoření adresáře. <br>Konkrétně se tato událost aktivuje, když klienti použijí operaci `CreateDirectory`, která je k dispozici ve Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft. Storage. DirectoryRenamed**|Aktivuje se při přejmenování adresáře. <br>Konkrétně se tato událost aktivuje, když klienti použijí operaci `RenameDirectory`, která je k dispozici ve Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft. Storage. DirectoryDeleted**|Aktivuje se, když se odstraní adresář. <br>Konkrétně se tato událost aktivuje, když klienti použijí operaci `DeleteDirectory`, která je k dispozici ve Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Chcete-li zajistit, aby se událost **Microsoft. Storage. BlobCreated** aktivovala pouze v případě, že je objekt blob bloku zcela potvrzen, vyfiltrujte událost pro volání REST API `FlushWithClose`. Toto volání rozhraní API aktivuje událost **Microsoft. Storage. BlobCreated** až po úplném potvrzení dat do objektu blob bloku. Další informace o tom, jak vytvořit filtr, najdete v tématu [filtrování událostí pro Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

Když se aktivuje událost, Služba Event Grid odešle data o této události do předplatného koncového bodu.

V této části najdete příklad toho, jak by tato data vypadala jako u každé události úložiště objektů BLOB.

### <a name="microsoftstorageblobcreated-event"></a>Událost Microsoft. Storage. BlobCreated

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Událost Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Pokud má účet Blob Storage hierarchický obor názvů, data budou vypadat podobně jako v předchozím příkladu s výjimkou těchto změn:

* Klíč `dataVersion` je nastaven na hodnotu `2`.

* Klíč `data.api` je nastaven na řetězec `CreateFile` nebo `FlushWithClose`.

* Do sady dat je zahrnut klíč `contentOffset`.

> [!NOTE]
> Pokud aplikace používá k nahrání nového objektu BLOB na účet operaci `PutBlockList`, data tyto změny neobsahují.

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

### <a name="microsoftstorageblobdeleted-event"></a>Událost Microsoft. Storage. BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Událost Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Pokud má účet Blob Storage hierarchický obor názvů, data budou vypadat podobně jako v předchozím příkladu s výjimkou těchto změn:

* Klíč `dataVersion` je nastaven na hodnotu `2`.

* Klíč `data.api` je nastaven na řetězec `DeleteFile`.

* Klíč `url` obsahuje cestu `dfs.core.windows.net`.

> [!NOTE]
> Pokud aplikace používají k odstranění objektu BLOB z účtu operaci `DeleteBlob`, data tyto změny nebudou obsahovat.

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

### <a name="microsoftstorageblobrenamed-event"></a>Událost Microsoft. Storage. BlobRenamed

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

### <a name="microsoftstoragedirectorycreated-event"></a>Událost Microsoft. Storage. DirectoryCreated

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Událost Microsoft. Storage. DirectoryRenamed

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Událost Microsoft. Storage. DirectoryDeleted

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

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | string | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Závislosti | string | Cesta definovaná vydavatelem k předmětu události |
| Typ | string | Jeden z registrovaných typů událostí pro tento zdroj události. |
| Čas události | string | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | string | Jedinečný identifikátor události |
| Údajů | object | Data události služby Blob Storage. |
| dataverze | string | Verze schématu datového objektu. Vydavatel definuje verzi schématu. |
| Verze metadataversion | string | Verze schématu metadat události. Event Grid definuje schéma vlastností nejvyšší úrovně. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| rozhraní api | string | Operace, která aktivovala událost. |
| ID žádosti klienta | string | ID požadavku pro rozhraní API úložiště poskytnuté klientem. Toto ID lze použít ke korelaci Azure Storage diagnostických protokolů pomocí pole "Client-Request-ID" v protokolech a lze je poskytnout v klientských požadavcích pomocí hlavičky x-MS-Client-Request-ID. Viz [Formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Identifikátor | string | ID žádosti generované službou pro operaci rozhraní API úložiště Dá se použít ke korelaci Azure Storage diagnostických protokolů pomocí pole "Request-ID-header" v protokolech a vrátí se z inicializace volání rozhraní API v hlavičce x-MS-Request-ID. Viz [Formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| značk | string | Hodnota, kterou můžete použít k podmíněnému provádění operací. |
| Třída | string | Typ obsahu zadaný pro objekt BLOB. |
| contentLength | celé číslo | Velikost objektu BLOB v bajtech |
| BlobType | string | Typ objektu BLOB Platné hodnoty jsou buď "BlockBlob" nebo "PageBlob". |
| contentOffset | číslo | Posun v bajtech operace zápisu provedené v místě, kde aplikace aktivující události dokončila zápis do souboru. <br>Zobrazí se jenom pro události aktivované v účtech BLOB Storage, které mají hierarchický obor názvů.|
| destinationUrl |string | Adresa URL souboru, který bude existovat po dokončení operace. Například pokud je soubor přejmenován, vlastnost `destinationUrl` obsahuje adresu URL nového názvu souboru. <br>Zobrazí se jenom pro události aktivované v účtech BLOB Storage, které mají hierarchický obor názvů.|
| sourceUrl |string | Adresa URL souboru, který existuje před operací. Například pokud je soubor přejmenován, `sourceUrl` obsahuje adresu URL původního názvu souboru před operací přejmenování. <br>Zobrazí se jenom pro události aktivované v účtech BLOB Storage, které mají hierarchický obor názvů. |
| url | string | Cesta k objektu BLOB <br>Pokud klient používá REST API objektů blob, pak adresa URL má tuto strukturu: *@no__t -1storage-Account-Name\>.blob.core.windows.net/\<container-Name @ no__t-4 @ no__t-5 @ no__t-6file-Name @ no__t-7*. <br>Pokud klient používá REST API Data Lake Storage, pak adresa URL má tuto strukturu: *@no__t -1storage-Account-Name\>.dfs.core.windows.net/\<file-System-Name @ no__t-4 @ no__t-5 @ no__t-6file-Name @ no__t-7*. |
| zahrnout | string | `True` pro provedení operace ve všech podřízených adresářích; jinak `False`. <br>Zobrazí se jenom pro události aktivované v účtech BLOB Storage, které mají hierarchický obor názvů. |
| Sequencer | string | Neprůhledná řetězcová hodnota představující logickou posloupnost událostí pro jakýkoliv konkrétní název objektu BLOB.  Uživatelé mohou použít standardní porovnání řetězců k pochopení relativní posloupnosti dvou událostí u stejného názvu objektu BLOB. |
| storageDiagnostics | object | Diagnostická data jsou občas součástí služby Azure Storage. V případě, že je k dispozici, by se měly ignorovat příjemci událostí. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostmi služby Blob Storage najdete v tématu [Směrování událostí služby Blob Storage – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
