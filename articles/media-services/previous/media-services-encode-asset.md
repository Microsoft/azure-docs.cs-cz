---
title: Přehled a porovnání kodérů médií vyžádání Azure | Dokumentace Microsoftu
description: Toto téma nabízí přehled a porovnání Azure na vyžádání kodérů médií.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: ed4b581cb962c7db706a71ab995963a3c445af55
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998145"
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders-legacy"></a>Přehled a porovnání Azure na vyžádání kodérů médií (starší verze)

## <a name="encoding-overview"></a>Kódování – přehled
Azure Media Services nabízí několik možností pro kódování multimédií v cloudu.

Když začínáte se službou Media Services, je důležité pochopit rozdíl mezi formáty kodeky a soubor.
Kodeky jsou software, který implementuje algoritmy komprese nebo dekomprese, zatímco formáty souborů jsou kontejnery, které obsahují komprimované video.

Služba Media Services poskytuje dynamické balení, což vám umožní dodávat váš obsah s adaptivní přenosovou rychlostí kódováním MP4 nebo technologie Smooth Streaming ve formátech streamování podporovaných službou Media Services (MPEG DASH, HLS, technologie Smooth Streaming), aniž byste je museli znovu zabalit do těchto formátů streamování.

> [!NOTE]
> Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Služba Media Services podporuje následující na vyžádání kodéry, které jsou popsány v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Pracovní postup kodéru Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Tento článek poskytuje stručný přehled na vyžádání kodérů médií a obsahuje odkazy na články, které poskytují podrobnější informace. V tématu taky najdete porovnání u kodérů.

>[!NOTE]
>Ve výchozím nastavení každý účet Media Services může mít jednu aktivní úlohu kódování najednou. Si můžete rezervovat kódovací jednotky, které vám umožní mít více úloh kódování, které jsou spuštěny souběžně, jeden pro každou kódování, kdy jste si koupili rezervovanou jednotku. Informace najdete v tématu [škálování jednotek pro kódování](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Způsob použití
[Kódování pomocí Media Encoderu Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formáty
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Předvolby
Media Encoder Standard je nakonfigurovaný pomocí jedné z přednastavení kodér popsané [tady](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Vstupní a výstupní metadata
Vstupní metadata kodérů popsaného [tady](media-services-input-metadata-schema.md).

Výstupní metadata kodérů popsaného [tady](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generování miniatur
Informace najdete v tématu [postup generování miniatur pomocí kodéru Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Oříznutí videa (výstřižek)
Informace najdete v tématu [jak k oříznutí videa pomocí kodéru Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Vytvořit překrytí
Informace najdete v tématu [Vytvoření překrytí pomocí kodéru Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Další informace najdete v tématech
[Blog služby Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Pracovní postup kodéru Media Encoder Premium
### <a name="overview"></a>Přehled
[Úvod do Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Způsob použití
Pracovní postup kodéru Media Encoder Premium je nakonfigurován pomocí komplexní pracovní postupy. Soubory pracovních postupů nemohl být vytvořen a aktualizovat pomocí [návrháře postupu provádění](media-services-workflow-designer.md) nástroj.

[Jak používat Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Známé problémy
Pokud vaše vstupní video neobsahuje titulků, výstup Asset bude stále obsahovat prázdný soubor TTML.


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Pokročilé kódovací úlohy provést pomocí přizpůsobení předvoleb Media Encoderu Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
