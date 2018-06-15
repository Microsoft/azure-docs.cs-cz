---
title: Postup vytvoření procesor médií pomocí Azure Media Services SDK pro .NET | Microsoft Docs
description: Naučte se vytvořit komponentu procesoru média zakódovat, převést formát, šifrování nebo dešifrování obsahu médií pro službu Azure Media Services. Ukázky kódu jsou napsané v jazyce C# a pomocí sady Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 60da450c11a2e65d96c15798854adfef371a694f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788190"
---
# <a name="how-to-get-a-media-processor-instance"></a>Postupy: získání Instance procesoru média
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
Ve službě Media Services, kterou procesor médií je komponenta, která zpracovává zpracování specifické pro úlohy, jako je například kódování formátu převodu, šifrování nebo dešifrování mediální obsah. Obvykle vytvoříte procesor médií při vytváření úlohy kódování, šifrování nebo převést formát mediální obsah.

## <a name="azure-media-processors"></a>Procesory médií Azure 

Následující téma obsahuje seznam procesory médií:

* [Procesory médií z kódování](scenarios-and-availability.md#encoding-media-processors)
* [Procesory médií Analytics](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Získat procesor médií

Následující metodu ukazuje, jak získat instance procesoru média. Příklad kódu předpokládá použití proměnné úroveň modulu s názvem **_kontextu** tak, aby odkazovaly kontextu serveru, jak je popsáno v části [postupy: připojení k Media Services prostřednictvím kódu programu](media-services-use-aad-auth-to-access-ams-api.md).

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
Teď, když víte, jak získat instance procesoru média, přejděte na [postup kódovat Asset](media-services-dotnet-encode-with-media-encoder-standard.md) téma, které vám ukáže, jak používat Media Encoder Standard kódování assetu.

