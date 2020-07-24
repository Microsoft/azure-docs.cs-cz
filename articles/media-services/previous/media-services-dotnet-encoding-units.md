---
title: Škálování zpracování médií přidáním jednotek kódování – Azure |  Microsoft Docs
description: Tento článek ukazuje, jak přidat jednotky kódování pomocí Azure Media Services .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milangada
ms.openlocfilehash: 49ba9b7a7f7cba883246bbfed055e22a4ea50b04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052905"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Jak škálovat kódování pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
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
