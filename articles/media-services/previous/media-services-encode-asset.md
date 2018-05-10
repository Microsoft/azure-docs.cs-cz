---
title: Přehled a porovnání Azure na vyžádání média kodéry | Microsoft Docs
description: Toto téma nabízí přehled a porovnání Azure na vyžádání kodéry média.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 6e44cff930f943f5cf30fb5335ad6a7fd1d08ab5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Přehled a porovnání Azure na vyžádání média kodéry
## <a name="encoding-overview"></a>Kódování – přehled
Azure Media Services poskytuje několik možností kódování média v cloudu.

Při spouštění pomocí služby Media Services, je důležité si uvědomit rozdíl mezi formáty kodeků a souboru.
Kodeky jsou software, který implementuje kompresi nebo dekompresi algoritmy, zatímco formáty souborů jsou kontejnery, které obsahují komprimované video.

Služba Media Services poskytuje dynamické balení, což vám umožní dodávat váš obsah s adaptivní přenosovou rychlostí s kódováním MP4 nebo technologie Smooth Streaming ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming), aniž byste je museli znovu zabalit do těchto formátů streamování.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. Chcete využít výhod [dynamické balení](media-services-dynamic-packaging-overview.md), musíte udělat následující:
>
>Navíc zakódujte váš zdrojový soubor do sady souborů MP4 s adaptivní přenosovou rychlostí nebo technologie Smooth Streaming soubory s adaptivní přenosovou rychlostí (postup kódování je ukázán později v tomto kurzu).

Služba Media Services podporuje následující na vyžádání kodéry, které jsou popsané v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Pracovní postup kodéru Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Tento článek poskytuje stručný přehled na vyžádání kodéry média a poskytuje odkazy na články, které poskytují podrobnější informace. V tématu taky najdete porovnání kodérů.

>[!NOTE]
>Ve výchozím nastavení může každý účet Media Services mít jeden aktivní kódování úkol najednou. Je možné rezervovat kódování jednotky, které vám umožní mít více kódování úloh spuštěných současně, jednu pro jednotlivé kódování vyhrazené jednotky, které jste si koupili. Informace najdete v tématu [škálování kódování jednotky](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Způsob použití
[Postup Kódovat pomocí kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formáty
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Přednastavení
Media Encoder Standard je konfigurován pomocí jedno z přednastavení kodér popsané [zde](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Vstup a výstup metadat
Vstupní metadata kodéry je popsán [zde](media-services-input-metadata-schema.md).

Výstup metadat kodéry je popsán [zde](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Vytváření miniatur
Informace najdete v tématu [jak vygenerovat miniatur pomocí kodéru Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Trim videa (výstřižek)
Informace najdete v tématu [postup trim videa pomocí kodéru Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Vytvoření překryvy
Informace najdete v tématu [vytvoření překryvy pomocí kodéru Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Další informace najdete v tématech
[Blog služby Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Pracovní postup kodéru Media Encoder Premium
### <a name="overview"></a>Přehled
[Představení Premium kódování v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Způsob použití
Media Encoder Premium pracovní postup je nakonfigurovat pomocí komplexní pracovních postupů. Soubory pracovního postupu by bylo možné vytvořit a aktualizovat pomocí [Návrhář postupu provádění](media-services-workflow-designer.md) nástroj.

[Jak používat Premium kódování v Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Známé problémy
Pokud vaše vstupní video neobsahuje uzavřen, přidávání titulků, výstup Asset bude stále obsahovat prázdný soubor TTML.


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Přizpůsobením Media Encoder Standard přednastavení provádět pokročilé úlohy kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
