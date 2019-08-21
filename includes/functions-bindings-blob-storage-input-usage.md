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
ms.openlocfilehash: 060a84c06e65441381a0cf74f20fb4a9d7b0f042
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642146"
---
Pro vstupní vazbu objektu blob můžete použít následující typy parametrů:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vyžaduje vázání `direction` "InOut" v *Function. JSON* nebo `FileAccess.ReadWrite` v knihovně C# tříd.

Pokud se pokusíte vytvořit propojení s jedním z typů sad SDK úložiště a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

Vazba na `string` nebo `Byte[]` je doporučena pouze v případě, že velikost objektu BLOB je malá, protože celý obsah objektu BLOB je načten do paměti. Obecně je vhodnější použít `Stream` typ nebo. `CloudBlockBlob` Další informace naleznete v části [využití souběžnosti a paměti](#trigger---concurrency-and-memory-usage) výše v tomto článku.
