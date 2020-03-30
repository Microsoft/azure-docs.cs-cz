---
title: Přehled kodéry médií Azure na vyžádání | Dokumenty společnosti Microsoft
description: Azure Media Services poskytuje několik možností pro kódování médií v cloudu. Tento článek poskytuje přehled kodéry médií Azure na vyžádání.
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
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251099"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Přehled kodéry médií Azure na vyžádání 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services poskytuje několik možností pro kódování médií v cloudu.

Při spuštění s Media Services, je důležité pochopit rozdíl mezi kodeky a formáty souborů.
Kodeky jsou software, který implementuje kompresní/dekompresní algoritmy, zatímco formáty souborů jsou kontejnery, které drží komprimované video.

Media Services poskytuje dynamické balení, které vám umožní poskytovat váš adaptivní datový tok MP4 nebo smooth streaming kódovaný obsah ve formátech streamování podporovaných Media Services (MPEG DASH, HLS, Smooth Streaming), aniž byste museli znovu zabalit do těchto formáty datových proudů.

Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. K fakturaci pro koncové body streamování dochází vždy, když je koncový bod ve stavu **Spuštěno.**

Služba Media Services podporuje následující kodéry na vyžádání, které jsou popsány v tomto článku:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Pracovní postup kodéru Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Tento článek poskytuje stručný přehled kodéry médií na vyžádání a obsahuje odkazy na články, které poskytují podrobnější informace. Téma také poskytuje porovnání kodéry.

Ve výchozím nastavení může mít každý účet služby Media Services současně jednu aktivní úlohu kódování. Můžete rezervovat jednotky kódování, které umožňují mít více úloh kódování spuštěných souběžně, jeden pro každou rezervovanou jednotku kódování, kterou zakoupíte. Další informace naleznete v tématu [Scaling coding units](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Způsob použití
[Jak kódovat pomocí standardu kodéru médií](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formáty
[Formáty a kodeky](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Přednastavení
Standard kodéru médií je konfigurován pomocí jednoho z předvoleb kodéru popsaných [zde](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Vstupní a výstupní metadata
Zde jsou [popsána](media-services-input-metadata-schema.md)vstupní metadata kodéry .

Zde jsou [popsána](media-services-output-metadata-schema.md)výstupní metadata kodéry .

### <a name="generate-thumbnails"></a>Generovat miniatury
Další informace naleznete v tématu [Jak generovat miniatury pomocí standardu kodéru médií](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Oříznutí videí (oříznutí)
Další informace naleznete v [tématu Jak oříznout videa pomocí standardu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Vytvoření překryvů
Další informace naleznete v tématu [Jak vytvořit překryvy pomocí standardu kodéru médií](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Viz také
[Blog Mediálních služeb](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Pracovní postup kodéru Media Encoder Premium
### <a name="overview"></a>Přehled
[Zavedení kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Způsob použití
Pracovní postup Premium pro kodéru médií je konfigurován pomocí složitých pracovních postupů. Soubory pracovního postupu lze vytvořit a aktualizovat pomocí nástroje [Návrhář pracovního postupu.](media-services-workflow-designer.md)

[Jak používat kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Známé problémy
Pokud vstupní video neobsahuje skryté titulky, bude výstupní datový zdroj stále obsahovat prázdný soubor TTML.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Související články
* [Provádění pokročilých úloh kódování přizpůsobením standardních přednastavení kodéru médií](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóty a omezení](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
