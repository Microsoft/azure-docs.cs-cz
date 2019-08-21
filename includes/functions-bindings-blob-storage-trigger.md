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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642159"
---
Pro triggerový objekt blob můžete použít následující typy parametrů:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO serializovatelný jako JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vyžaduje vázání `direction` "InOut" v *Function. JSON* nebo `FileAccess.ReadWrite` v knihovně C# tříd.

Pokud se pokusíte vytvořit propojení s jedním z typů sad SDK úložiště a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](#azure-storage-sdk-version-in-functions-1x).

Vazba na `string`, `Byte[]`nebo POCO se doporučuje pouze v případě, že je velikost objektu BLOB malá, protože celý obsah objektu BLOB je načten do paměti. Obecně je vhodnější použít `Stream` typ nebo. `CloudBlockBlob` Další informace najdete v části [využití souběžnosti a paměti](#trigger---concurrency-and-memory-usage) dále v tomto článku.