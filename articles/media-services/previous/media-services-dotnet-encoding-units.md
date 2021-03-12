---
title: Škálování zpracování médií přidáním jednotek kódování – Azure |  Microsoft Docs
description: Tento článek ukazuje, jak přidat jednotky kódování pomocí Azure Media Services .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: 6feb28858271f11bef0933a352f86d7545ee5bd3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014056"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Jak škálovat kódování pomocí sady .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Přehled
> [!IMPORTANT]
> Nezapomeňte si projít [Přehled](media-services-scale-media-processing-overview.md) a získat další informace o škálování zpracování multimédií.
> 
> 

Pokud chcete změnit typ rezervované jednotky a počet jednotek rezervovaných pro kódování pomocí sady .NET SDK, udělejte toto:

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>Otevření lístku podpory

Ve výchozím nastavení se každý účet Media Services může škálovat až na 10 jednotek nebo jednotek rezervovaných pro média (MRUs) nebo na 25 S1 MRUs a na jednotky rezervované pro streamování na vyžádání. Vyšší limit si můžete vyžádat otevřením lístku podpory.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
