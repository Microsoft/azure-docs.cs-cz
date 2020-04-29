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
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77202096"
---
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

<sup>1</sup> vyžaduje vazbu `direction` "in" v *Function. JSON* nebo `FileAccess.Read` v knihovně tříd jazyka C#. Můžete však použít objekt kontejneru, který modul runtime poskytuje k provádění operací zápisu, jako je například nahrání objektů blob do kontejneru.

<sup>2</sup> vyžaduje vazbu `direction` "InOut" ve *funkci Function. JSON* nebo `FileAccess.ReadWrite` v knihovně tříd jazyka C#.

Pokud se pokusíte vytvořit propojení s jedním z typů sad SDK úložiště a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

V asynchronních funkcích použijte návratovou hodnotu nebo `IAsyncCollector` místo `out` parametru.

Vazba na `string` nebo `Byte[]` je doporučena pouze v případě, že velikost objektu BLOB je malá, protože celý obsah objektu BLOB je načten do paměti. Obecně je vhodnější použít typ `Stream` nebo. `CloudBlockBlob` Další informace naleznete v části [využití souběžnosti a paměti](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) výše v tomto článku.
