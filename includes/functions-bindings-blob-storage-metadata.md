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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455906"
---
Trigger objektu BLOB poskytuje několik vlastností metadat. Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako typ [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) .

|Vlastnost  |Typ  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Cesta k aktivovanému objektu BLOB.|
|`Uri`|`System.Uri`|Identifikátor URI objektu BLOB pro primární umístění|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Vlastnosti systému objektu BLOB. |
|`Metadata` |`IDictionary<string,string>`|Uživatelsky definovaná metadata pro objekt BLOB.|

Například následující příklady skriptu jazyka C# a jazyka JavaScript protokolují cestu k aktivovanému objektu blob, včetně kontejneru:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```