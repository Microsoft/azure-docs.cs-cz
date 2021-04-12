---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100381646"
---
### <a name="default"></a>Výchozí

Můžete vytvořit vazby na následující typy pro zápis objektů BLOB:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>první</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>odst</sup>
* `CloudBlockBlob`<sup>odst</sup>
* `CloudPageBlob`<sup>odst</sup>
* `CloudAppendBlob`<sup>odst</sup>

<sup>1</sup> vyžaduje vázání "in" `direction` v *function.jsna* nebo `FileAccess.Read` v knihovně tříd C#. Můžete však použít objekt kontejneru, který modul runtime poskytuje k provádění operací zápisu, jako je například nahrání objektů blob do kontejneru.

<sup>2</sup> vyžaduje vázání "InOut" `direction` v *function.js* v `FileAccess.ReadWrite` knihovně tříd C#.

Pokud se pokusíte vytvořit propojení s jedním z typů sad SDK úložiště a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Vazba na `string` nebo `Byte[]` je doporučena pouze v případě, že velikost objektu BLOB je malá, protože celý obsah objektu BLOB je načten do paměti. Obecně je vhodnější použít `Stream` `CloudBlockBlob` typ nebo. Další informace naleznete v části [využití souběžnosti a paměti](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) výše v tomto článku.

### <a name="additional-types"></a>Další typy

Aplikace používající [5.0.0 nebo vyšší verze rozšíření úložiště](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) můžou používat taky typy ze [sady Azure SDK pro .NET](/dotnet/api/overview/azure/storage.blobs-readme). Tato verze vyřazuje podporu pro starší `CloudBlobContainer` typy, `CloudBlobDirectory` ,,, `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` a, a to `CloudAppendBlob` ve prospěch z následujících typů:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> vyžaduje vázání "in" `direction` v *function.jsna* nebo `FileAccess.Read` v knihovně tříd C#. Můžete však použít objekt kontejneru, který modul runtime poskytuje k provádění operací zápisu, jako je například nahrání objektů blob do kontejneru.

<sup>2</sup> vyžaduje vázání "InOut" `direction` v *function.js* v `FileAccess.ReadWrite` knihovně tříd C#.

Příklady použití těchto typů najdete v [úložišti GitHub pro rozšíření](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
