---
title: Jak vytvořit mediální procesor pomocí sady Azure Media Services SDK pro rozhraní .NET| Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit komponentu mediálního procesoru pro kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu pro Azure Media Services. Ukázky kódu jsou zapsány v c# a používají sady Media Services SDK pro rozhraní .NET.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463826"
---
# <a name="how-to-get-a-media-processor-instance"></a>Postup: Získání instance mediálního procesoru
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Odpočinku](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Přehled
V media services je mediální procesor součástí, která zpracovává určitou úlohu zpracování, jako je kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Při vytváření úlohy kódování, šifrování nebo převodu formátu mediálního obsahu obvykle vytváříte mediální procesor.

## <a name="azure-media-processors"></a>Mediální procesory Azure 

Následující téma obsahuje seznam mediálních procesorů:

* [Kódovací procesory médií](scenarios-and-availability.md#encoding-media-processors)
* [Analytické procesory médií](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Získat mediální procesor

Následující metoda ukazuje, jak získat instanci mediálního procesoru. Příklad kódu předpokládá použití proměnné na úrovni modulu s názvem **_context** odkazovat na kontext serveru, jak je popsáno v části [Jak: Připojit k mediální služby programově](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Nyní, když víte, jak získat instanci mediálního procesoru, přejděte na téma [Jak zakódovat datový zdroj,](media-services-dotnet-encode-with-media-encoder-standard.md) které vám ukáže, jak pomocí standardu kodéru médií kódovat datový zdroj.

