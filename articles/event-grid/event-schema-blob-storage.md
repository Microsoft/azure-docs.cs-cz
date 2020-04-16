---
title: Azure Blob Storage jako zdroj mřížky událostí
description: Popisuje vlastnosti, které jsou k dispozici pro události úložiště objektů blob pomocí Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: cfc6e4790b67137b423cc90d93874d4914f81251
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393373"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure Blob Storage jako zdroj mřížky událostí

Tento článek obsahuje vlastnosti a schéma pro události úložiště objektů blob.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md). Poskytuje také seznam rychlých spuštění a kurzů pro použití Azure Blob Storage jako zdroj událostí.


>[!NOTE]
> Pouze účty úložiště druhu **StorageV2 (pro obecné účely v2)**, **BlockBlobStorage**a Integrace událostí podpory **BlobStorage.** **Úložiště (genral účel v1)** *nepodporuje* integraci s Event Grid.

## <a name="event-grid-event-schema"></a>Schéma události Mřížka událostí

### <a name="list-of-events-for-blob-rest-apis"></a>Seznam událostí pro api objektů BLOB REST

Tyto události se aktivují, když klient vytvoří, nahradí nebo odstraní objekt blob voláním objektů API B REST.

 |Název události |Popis|
 |----------|-----------|
 |**Microsoft.Storage.BlobVytvořeno** |Aktivuje se při vytvoření nebo nahrazení objektu blob. <br>Konkrétně tato událost se aktivuje, `PutBlob` `PutBlockList`když `CopyBlob` klienti používají , nebo operace, které jsou k dispozici v rozhraní BLOB REST API.   |
 |**Soubor Microsoft.Storage.BlobOdstraněn** |Aktivuje se při odstranění objektu blob. <br>Konkrétně tato událost se aktivuje, `DeleteBlob` když klienti volání operace, která je k dispozici v rozhraní API b/ blob REST. |

> [!NOTE]
> Pokud chcete zajistit, že **Microsoft.Storage.BlobCreated** událost se aktivuje pouze v případě, že `CopyBlob`objekt `PutBlob`blob bloku je zcela potvrzena, filtrovat událost pro volání rozhraní API a `PutBlockList` REST. Tato volání rozhraní API aktivují událost **Microsoft.Storage.BlobCreated** až poté, co jsou data plně potvrzena pro objekt blob bloku. Informace o vytvoření filtru naleznete v tématu [Filtrování událostí pro mřížku událostí](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Seznam událostí pro azure data lake storage gen 2 rest api

Tyto události se aktivují, pokud povolíte hierarchický obor názvů v účtu úložiště a klienti volají Azure Data Lake Storage Gen2 REST API. Další informace o azure data lake storage gen2, najdete [v tématu Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Název události|Popis|
|----------|-----------|
|**Microsoft.Storage.BlobVytvořeno** | Aktivuje se při vytvoření nebo nahrazení objektu blob. <br>Konkrétně tato událost se aktivuje, `CreateFile` `FlushWithClose` když klienti používají a operace, které jsou k dispozici v rozhraní API Azure Data Lake Storage Gen2 REST. |
|**Soubor Microsoft.Storage.BlobOdstraněn** |Aktivuje se při odstranění objektu blob. <br>Konkrétně Tato událost se aktivuje také `DeleteFile` při volání klientů operace, která je k dispozici v rozhraní API Azure Data Lake Storage Gen2 REST. |
|**Microsoft.Storage.BlobRenamed**|Aktivuje se při přejmenování objektu blob. <br>Konkrétně tato událost se aktivuje, `RenameFile` když klienti používají operaci, která je k dispozici v rozhraní API AZURE Data Lake Storage Gen2 REST.|
|**Soubor Microsoft.Storage.DirectoryVytvořeno**|Aktivuje se při vytvoření adresáře. <br>Konkrétně tato událost se aktivuje, `CreateDirectory` když klienti používají operaci, která je k dispozici v rozhraní API AZURE Data Lake Storage Gen2 REST.|
|**Microsoft.Storage.DirectoryPřejmenován**|Aktivuje se při přejmenování adresáře. <br>Konkrétně tato událost se aktivuje, `RenameDirectory` když klienti používají operaci, která je k dispozici v rozhraní API AZURE Data Lake Storage Gen2 REST.|
|**Soubor Microsoft.Storage.DirectoryOdstraněn**|Aktivuje se při odstranění adresáře. <br>Konkrétně tato událost se aktivuje, `DeleteDirectory` když klienti používají operaci, která je k dispozici v rozhraní API AZURE Data Lake Storage Gen2 REST.|

> [!NOTE]
> Pokud chcete zajistit, že **Microsoft.Storage.BlobCreated** událost se aktivuje pouze v případě, že `FlushWithClose` objekt blob bloku je zcela potvrzena, filtrovat událost pro volání rozhraní API REST. Toto volání rozhraní API aktivuje událost **Microsoft.Storage.BlobCreated** až poté, co jsou data plně potvrzena pro objekt blob bloku. Informace o vytvoření filtru naleznete v tématu [Filtrování událostí pro mřížku událostí](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

### <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

Když je událost spuštěna, služba Event Grid odešle data o této události do koncového bodu přihlášení.

Tato část obsahuje příklad toho, co by tato data vypadat pro každou událost úložiště objektů blob.

### <a name="microsoftstorageblobcreated-event"></a>Událost Microsoft.Storage.BlobCreated

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Událost Microsoft.Storage.BlobCreated (Úložiště datového jezera Gen2)

Pokud má účet úložiště objektů blob hierarchický obor názvů, data vypadají podobně jako v předchozím příkladu s výjimkou těchto změn:

* Klíč `dataVersion` je nastaven na `2`hodnotu .

* Klíč `data.api` je nastaven na `CreateFile` `FlushWithClose`řetězec nebo .

* Klíč `contentOffset` je součástí sady dat.

> [!NOTE]
> Pokud aplikace `PutBlockList` pomocí operace k nahrání nového objektu blob do účtu, data nebudou obsahovat tyto změny.

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobdeleted-event"></a>Událost Microsoft.Storage.BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Událost Microsoft.Storage.BlobDeleted (Úložiště datového jezera Gen2)

Pokud má účet úložiště objektů blob hierarchický obor názvů, data vypadají podobně jako v předchozím příkladu s výjimkou těchto změn:

* Klíč `dataVersion` je nastaven na `2`hodnotu .

* Klíč `data.api` je nastaven na `DeleteFile`řetězec .

* Klíč `url` obsahuje cestu `dfs.core.windows.net`.

> [!NOTE]
> Pokud aplikace `DeleteBlob` pomocí operace odstranit objekt blob z účtu, data nebudou obsahovat tyto změny.

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

### <a name="microsoftstorageblobrenamed-event"></a>Událost Microsoft.Storage.BlobRenamed

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

### <a name="microsoftstoragedirectorycreated-event"></a>Událost Microsoft.Storage.DirectoryCreated

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Událost Microsoft.Storage.DirectoryPřejmenovaná událost

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Událost Microsoft.Storage.DirectoryOdstraněn

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

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí úložiště objektů blob. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| rozhraní api | řetězec | Operace, která spustila událost. |
| klientRequestId | řetězec | id požadavku poskytnutého klientem pro operaci rozhraní API úložiště. Toto id lze použít ke korelaci s protokoly diagnostiky úložiště Azure pomocí pole "client-request-id" v protokolech a lze je poskytnout v požadavcích klientů pomocí hlavičky "x-ms-client-request-id". Viz [Formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Requestid | řetězec | ID požadavku generované službou pro operaci rozhraní API úložiště. Slouží ke korelaci s diagnostickými protokoly úložiště Azure pomocí pole "request-id-header" v protokolech a je vrácena z zahájení volání rozhraní API v záhlaví 'x-ms-request-id'. Viz [Formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | řetězec | Hodnota, kterou můžete použít k podmíněnému provádění operací. |
| Contenttype | řetězec | Typ obsahu zadaný pro objekt blob. |
| Contentlength | celé číslo | Velikost objektu blob v bajtů. |
| objekt blobType | řetězec | Typ objektu blob. Platné hodnoty jsou buď "BlockBlob" nebo "PageBlob". |
| contentOffset | číslo | Posun v bajtů operace zápisu přijata v místě, kde událost spouštějící aplikace dokončena zápisu do souboru. <br>Zobrazí se pouze pro události aktivované na účtech úložiště objektů blob, které mají hierarchický obor názvů.|
| cílUrl |řetězec | Adresa URL souboru, který bude existovat po dokončení operace. Pokud je například soubor přejmenován, `destinationUrl` vlastnost obsahuje adresu URL nového názvu souboru. <br>Zobrazí se pouze pro události aktivované na účtech úložiště objektů blob, které mají hierarchický obor názvů.|
| sourceUrl |řetězec | Adresa URL souboru, který existuje před operací. Pokud je například soubor přejmenován, `sourceUrl` obsahuje adresu URL původního názvu souboru před operací přejmenování. <br>Zobrazí se pouze pro události aktivované na účtech úložiště objektů blob, které mají hierarchický obor názvů. |
| url | řetězec | Cesta k objektu blob. <br>Pokud klient používá rozhraní BLOB REST API, pak adresa URL má tuto strukturu: * \<název účtu\>úložiště .blob.core.windows.net/\<název\>/\<\>souboru .* <br>Pokud klient používá rozhraní REST API úložiště datového jezera, pak adresa URL má tuto strukturu: * \<název účtu úložiště\>.dfs.core.windows.net/\<název\>/\<\>souboru-název souboru*. |
| Rekurzivní | řetězec | `True`provádět operaci na všech podřízených adresářích; v `False`opačném případě . <br>Zobrazí se pouze pro události aktivované na účtech úložiště objektů blob, které mají hierarchický obor názvů. |
| Sekvencer | řetězec | Neprůhledná hodnota řetězce představující logickou posloupnost událostí pro libovolný konkrétní název objektu blob.  Uživatelé mohou použít standardní porovnání řetězců k pochopení relativní posloupnosti dvou událostí na stejném názvu objektu blob. |
| storageDiagnostika | objekt | Diagnostická data občas zahrnutá službou Azure Storage. Pokud je k dispozici, by měly být ignorovány příjemci událostí. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: směrování událostí úložiště objektů Blob do vlastního webového koncového bodu pomocí azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k odesílání událostí úložiště objektů blob na WebHook. |
| [Úvodní příručka: směrování událostí úložiště objektů Blob do vlastního webového koncového bodu pomocí Prostředí PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure PowerShell k odesílání událostí úložiště objektů blob na WebHook. |
| [Úvodní příručka: vytváření a směrování událostí úložiště objektů Blob pomocí portálu Azure](blob-event-quickstart-portal.md) | Ukazuje, jak pomocí portálu odesílat události úložiště objektů blob na WebHook. |
| [Nastavení příkazu k řešení Azure: přihlášení k odběru událostí pro účet úložiště objektů Blob](./scripts/event-grid-cli-blob.md) | Ukázkový skript, který se přihlásí k odběru události pro účet úložiště objektů Blob. Odešle událost webhooku. |
| [PowerShell: Přihlášení k odběru událostí pro účet úložiště objektů Blob](./scripts/event-grid-powershell-blob.md) | Ukázkový skript, který se přihlásí k odběru události pro účet úložiště objektů Blob. Odešle událost webhooku. |
| [Šablona Správce prostředků: Vytvoření úložiště objektů Blob a předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. Odesílá události webhooku. |
| [Přehled: Reakce na události úložiště objektů Blob](../storage/blobs/storage-blob-event-overview.md) | Přehled integrace úložiště objektů Blob s událostí. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostmi úložiště objektů blob najdete v [tématu události úložiště objektů blob trasy – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
