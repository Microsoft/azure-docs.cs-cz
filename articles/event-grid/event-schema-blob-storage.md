---
title: Azure Event Grid objektu blob úložiště událostí schématu
description: Popisuje vlastnosti, které jsou k dispozici pro události služby blob storage pomocí služby Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 11524f8868a0102e30b06f3385a26b1bd06aae6e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055447"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schéma událostí Azure Event Grid pro úložiště objektů Blob

Tento článek obsahuje vlastnosti a schéma pro události služby blob storage. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj události úložiště](event-sources.md#storage).

## <a name="available-event-types"></a>Typy událostí k dispozici

Úložiště objektů BLOB generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Vyvolá se při vytvoření objektu blob. |
| Microsoft.Storage.BlobDeleted | Vyvolá se při odstranění objektu blob. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schématu objektu blob vytvoří událost: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
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
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Se podobá schéma pro objekt blob odstranit událost: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| Předmět | řetězec | Vydavatel definované cesta předmět události. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události úložiště objektů BLOB. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| rozhraní api | řetězec | Operace, který spustil danou událost. |
| ID žádosti klienta | řetězec | Klientem generovaná, neprůhledná hodnota se limit znaků 1 KB. Pokud jste povolili protokolování analýzy úložiště, se zaznamená do protokoly analýzy. |
| ID žádosti | řetězec | Jedinečný identifikátor pro daný požadavek. Použijte pro řešení potíží s požadavku. |
| značka eTag | řetězec | Hodnota, která můžete použít k provádění operací podmíněně. |
| contentType | řetězec | Pro tento objekt blob zadaný typ obsahu. |
| contentLength | integer | Velikost objektu blob v bajtech. |
| BlobType | řetězec | Typ objektu blob. Platné hodnoty jsou "BlockBlob" nebo "PageBlob". |
| url | řetězec | Cesta k objektu blob. |
| aplikace Sequencer | řetězec | Řízené uživatelem hodnotu, která můžete použít ke sledování požadavků. |
| storageDiagnostics | objekt | Informace o diagnostice úložiště. |
 
## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostí služby blob storage, najdete v článku [trasy objektu Blob úložiště události – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
