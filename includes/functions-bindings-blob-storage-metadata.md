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
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998823"
---
Trigger objektu BLOB poskytuje několik vlastností metadat. Tyto vlastnosti lze použít jako součást výrazů vazby v jiných vazbách nebo jako parametry v kódu. Tyto hodnoty mají stejnou sémantiku jako typ [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

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