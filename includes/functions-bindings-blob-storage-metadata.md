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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642133"
---
Trigger objektu BLOB poskytuje několik vlastností metadat. Tyto vlastnosti lze použít jako součást výrazy vazby v jiných vazbách nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako typ [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

|Vlastnost  |Typ  |Popis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Cesta k aktivovanému objektu BLOB.|
|`Uri`|`System.Uri`|Identifikátor URI objektu BLOB pro primární umístění|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Vlastnosti systému objektu BLOB. |
|`Metadata` |`IDictionary<string,string>`|Uživatelsky definovaná metadata pro objekt BLOB.|

Například následující C# příklady skriptu a JavaScriptu protokolují cestu k aktivovanému objektu blob, včetně kontejneru:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
