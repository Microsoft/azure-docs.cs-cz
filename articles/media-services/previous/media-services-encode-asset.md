---
title: Přehled kodérů médií v Azure na vyžádání | Microsoft Docs
description: Azure Media Services poskytuje více možností pro kódování médií v cloudu. Tento článek obsahuje přehled kodérů médií na vyžádání v Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: e841d1922beb3f0276ff94cacbbdf566b23cdf6e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017280"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Přehled kodérů médií na vyžádání v Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Azure Media Services poskytuje více možností pro kódování médií v cloudu.

Když začnete s Media Services, je důležité pochopit rozdíl mezi kodeky a formáty souborů.
Kodeky jsou software, který implementuje algoritmy komprese/dekomprese, zatímco formáty souborů jsou kontejnery, které obsahují komprimované video.

Media Services poskytuje dynamické balení, které vám umožní doručovat obsah MP4 s adaptivní přenosovou rychlostí nebo Smooth Streaming kódovaný obsah ve formátech streamování podporovaných Media Services (MPEG POMLČKa, HLS, Smooth Streaming), aniž byste je museli znovu zabalit do těchto formátů streamování.

Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. K koncovým bodům streamování dojde pokaždé, když je koncový bod ve **spuštěném** stavu.

Media Services podporuje následující kodér na vyžádání:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Tento článek poskytuje stručný přehled kodérů médií na vyžádání a odkazy na články s podrobnějšími informacemi.

Ve výchozím nastavení každý účet Media Services může mít současně jednu aktivní úlohu kódování. Můžete rezervovat jednotky kódování, které vám umožní mít souběžně spuštěné více úloh kódování. jednu pro každou rezervovanou jednotku kódování zakoupíte. Informace najdete v tématu [škálování jednotek kódování](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Způsob použití
[Postup při kódování pomocí Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formáty
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Žádném
Media Encoder Standard se konfiguruje pomocí některého z přednastavených kodérů popsaných [tady](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Vstupní a výstupní metadata
Metadata vstupu kodérů jsou popsána [zde](media-services-input-metadata-schema.md).

[Zde](media-services-output-metadata-schema.md)jsou popsána metadata pro kodéry.

### <a name="generate-thumbnails"></a>Generovat miniatury
Informace najdete v tématu [jak generovat miniatury pomocí Media Encoder Standard](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Střih videí (oříznutí)
Informace najdete v tématu [Postup při stříhání videí pomocí Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Vytvořit překryvy
Informace najdete v tématu [jak vytvořit překryvy pomocí Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Viz také
[Blog Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Známé problémy
Pokud vstupní video neobsahuje skryté titulky, bude výstupní Asset stále obsahovat prázdný soubor TTML.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením Media Encoder Standard přednastavení](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/