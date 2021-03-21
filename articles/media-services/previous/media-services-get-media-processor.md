---
title: Jak vytvořit procesor médií pomocí sady Azure Media Services SDK pro .NET | Microsoft Docs
description: Naučte se vytvořit komponentu procesoru médií ke kódování, převedení formátu, šifrování nebo dešifrování mediálního obsahu pro Azure Media Services. Ukázky kódu jsou napsané v jazyce C# a používají sadu Media Services SDK pro .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013342"
---
# <a name="how-to-get-a-media-processor-instance"></a>Postupy: získání instance procesoru multimédií

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Přehled

V Media Services procesor médií je komponenta, která zpracovává konkrétní úlohu zpracování, jako je například kódování, převod formátu, šifrování nebo dešifrování mediálního obsahu. Procesor médií se obvykle vytváří při vytváření úlohy pro kódování, šifrování nebo převod formátu mediálního obsahu.

## <a name="azure-media-processors"></a>Procesory médií Azure

V následujícím tématu najdete seznam procesorů médií:

* [Kódovací procesory médií](scenarios-and-availability.md)
* [Analytické procesory médií](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Získat procesor médií

Následující metoda ukazuje, jak získat instanci procesoru médií. Příklad kódu předpokládá použití proměnné na úrovni modulu s názvem **_context** pro odkazování na kontext serveru, jak je popsáno v části [Postupy: připojení k Media Services programově](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky

Když teď víte, jak získat instanci procesoru médií, přečtěte si téma [Postup kódování assetu](media-services-dotnet-encode-with-media-encoder-standard.md) , ve kterém se dozvíte, jak použít Media Encoder Standard ke kódování assetu.