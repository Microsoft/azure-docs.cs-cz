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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "69642133"
---
Aktivační událost objektu blob poskytuje několik vlastností metadat. Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazeb nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako typ [CloudBlob.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)

|Vlastnost  |Typ  |Popis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Cesta k aktivační objekt blob.|
|`Uri`|`System.Uri`|Identifikátor URI objektu BLOB pro primární umístění.|
|`Properties` |[Vlastnosti objektu Blob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Vlastnosti systému objektu blob. |
|`Metadata` |`IDictionary<string,string>`|Uživatelem definovaná metadata pro objekt blob.|

Například následující skript jazyka C# a příklady jazyka JavaScript protokolují cestu k aktivačnímu objektu blob, včetně kontejneru:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
