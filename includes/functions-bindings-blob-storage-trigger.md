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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202109"
---
Pro aktivační objekt blob můžete použít následující typy parametrů:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Poco serializovatelný jako JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Vyžaduje vazbu `direction` "inout" v `FileAccess.ReadWrite` *function.json* nebo v knihovně tříd c#.

Pokud se pokusíte vytvořit vazbu na jeden z typů sady Storage SDK a zobrazí se chybová zpráva, ujistěte se, že máte odkaz na [správnou verzi sady Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Vazba `string` `Byte[]`na , , nebo POCO se doporučuje pouze v případě, že velikost objektu blob je malá, jako celý obsah objektu blob jsou načteny do paměti. Obecně je vhodnější použít `Stream` typ `CloudBlockBlob` nebo. Další informace naleznete [v tématu Souběžnost a využití paměti](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) dále v tomto článku.