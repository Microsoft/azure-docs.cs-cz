---
title: Škálovat přidáním jednotek pro kódování – Azure zpracování médií |  Dokumentace Microsoftu
description: Přečtěte si postup pro přidání jednotky kódování s využitím .NET
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
ms.date: 02/09/2019
ms.author: juliako;milangada;
ms.openlocfilehash: e97f2c4985d41626829c7899d70391cc155f01b4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990390"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Jak škálovat kódování pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Přehled
> [!IMPORTANT]
> Přečtěte si [přehled](media-services-scale-media-processing-overview.md) zobrazíte další informace o škálování zpracování médií.
> 
> 

Změna typu rezervované jednotky a počet pomocí sady .NET SDK jednotky rezervované pro kódování, postupujte takto:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Otevření lístku podpory

Ve výchozím nastavení každý účet Media Services můžete škálovat až 10 S2 nebo rezervované jednotky médií S3 (použité položky) nebo 25 použité položky S1 a 5 na vyžádání jednotky rezervované pro streamování. Vyšší limit můžete požádat tak, že otevřete lístek podpory.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

