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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "69642133"
---
Trigger objektu BLOB poskytuje několik vlastností metadat. Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako typ [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

|Vlastnost  |Typ  |Popis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Cesta k aktivovanému objektu BLOB.|
|`Uri`|`System.Uri`|Identifikátor URI objektu BLOB pro primární umístění|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Vlastnosti systému objektu BLOB. |
|`Metadata` |`IDictionary<string,string>`|Uživatelsky definovaná metadata pro objekt BLOB.|

Například následující příklady skriptu jazyka C# a jazyka JavaScript protokolují cestu k aktivovanému objektu blob, včetně kontejneru:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
