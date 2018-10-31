---
title: Jak vytvořit procesor médií pomocí Azure Media Services SDK pro .NET | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit komponentu procesor médií pro kódování, převod formátu, šifrování nebo dešifrování obsahu médií pro Azure Media Services. Ukázky kódu jsou napsané C# a používat Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 64e353bbb83c7696960fc1d2e478478afbc94241
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249233"
---
# <a name="how-to-get-a-media-processor-instance"></a>Postupy: získání Instance procesoru médií
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
Ve službě Media Services, které procesor médií je komponenta, která zpracovává konkrétní zpracování úloh, jako je například kódování formát převodu, šifrování nebo dešifrování obsahu médií. Obvykle vytvoříte procesor médií, při vytváření úlohy kódování, šifrování nebo převést formát mediálního obsahu.

## <a name="azure-media-processors"></a>Procesory médií Azure 

V následujícím tématu najdete seznam procesory médií:

* [Kódovací procesory médií](scenarios-and-availability.md#encoding-media-processors)
* [Analytické procesory médií](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Získat procesor médií

Následující metoda ukazuje, jak získat instanci procesoru médií. Příklad kódu předpokládá použití úrovni modulu proměnnou s názvem **_kontext** odkazovat kontextu serveru, jak je popsáno v části [postupy: připojení k Media Services prostřednictvím kódu programu](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Teď, když víte, jak získat instanci procesor médií, přejděte [kódování Assetu](media-services-dotnet-encode-with-media-encoder-standard.md) tématu, kde se dozvíte, jak kódování prostředku pomocí Media Encoderu Standard.

