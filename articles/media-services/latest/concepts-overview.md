---
title: Terminologie a pojmy mediálních služeb
titleSuffix: Azure Media Services
description: Seznamte se s terminologií a koncepty pro Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500086"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologie a pojmy mediálních služeb

Toto téma poskytuje stručný přehled terminologie a konceptů Azure Media Services. Článek také obsahuje odkazy na články s podrobným vysvětlením konceptů a funkcí služby Media Services v3.

Základní pojmy popsané v těchto tématech by měly být před zahájením vývoje přezkoumány.

> [!NOTE]
> V současné době můžete na [webu Azure Portal:](https://portal.azure.com/) spravovat živé [události](live-events-outputs-concept.md)Služby Media Services v3 , zobrazovat (nespravovat) [datové zdroje](assets-concept.md)v3 a získat informace [o přístupu k virtuálním i matovým icím](access-api-portal.md).
> Pro všechny ostatní úlohy správy (například [Transformace a úlohy](transforms-jobs-concept.md) a [ochrana obsahu](content-protection-overview.md)) použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologie Media Services v3

|Označení|Popis|
|---|---|
|Živá událost|**Živá událost** představuje kanál pro ingestování, překódování (volitelně) a balení živých datových proudů videa, zvuku a metadat v reálném čase.<br/><br/>Pro zákazníky migrující z media services v2 API, **live událost** nahradí entity **kanálu** v v2. Další informace naleznete [v tématu Migrace z v2 na v3](migrate-from-v2-to-v3.md).|
|Koncový bod streamování/balení/původ|Koncový **bod streamování** představuje dynamickou službu balení a původu (just-in-time), která může dodávat váš živý obsah a obsah na vyžádání přímo do aplikace klientského přehrávače. Používá jeden z běžných protokolů datových proudů médií (HLS nebo DASH). Koncový bod **streamování** navíc poskytuje dynamické šifrování (just-in-time) špičkovým systémům správy digitálních práv (DRM).<br/><br/>V odvětví streamování médií se tato služba běžně označuje jako **Packager** nebo **Origin**.  Další běžné termíny v oboru pro tuto schopnost patří JITP (just-in-time-packager) nebo JITE (just-in-time-encryption).

## <a name="media-services-v3-concepts"></a>Koncepty Media Services v3

|Koncepty|Popis|Odkazy|
|---|---|---|
|Datové zdroje a nahrávání obsahu|Chcete-li začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Mediální chslužeb a nahrát digitální soubory do **datových zdrojů**.|[Nahrávání na cloud a úložiště](storage-account-concept.md)<br/><br/>[Koncepce aktiv](assets-concept.md)|
|Kódování obsahu|Jakmile nahrajete vysoce kvalitní soubory digitálních médií do datových zdrojů, můžete je zakódovat do formátů, které lze přehrávat v široké škále prohlížečů a zařízení. <br/><br/>Chcete-li kódovat pomocí služby Media Services v3, je třeba vytvořit **transformace** a **úlohy**.|[Transformace a úlohy](transforms-jobs-concept.md)<br/><br/>[Kódování pomocí mediálních služeb](encoding-concept.md)|
|Analýza obsahu (Video Indexer)|Služba Media Services v3 umožňuje extrahovat přehledy z video souborů a zvukových souborů pomocí předvoleb Media Services v3. Chcete-li analyzovat obsah pomocí přednastavení Media Services v3, je třeba vytvořit **transformace** a **úlohy**.<br/><br/>Pokud chcete podrobnější přehledy, použijte video [indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) přímo.|[Analýza video souborů a zvukových souborů](analyzing-video-audio-files-concept.md)|
|Balení a doručování|Po zakódování obsahu můžete využít výhod **dynamického balení**. Ve službě Media Services je **koncový bod streamování** dynamickou balicí službou používanou k doručování mediálního obsahu přehrávačům klientů. Chcete-li zpřístupnit videa ve výstupním datovém zdroji klientům pro přehrávání, musíte vytvořit **lokátor streamování** a potom vytvořit adresy URL streamování. <br/><br/>Při vytváření **lokátoru datových proudů**je třeba kromě názvu datového zdroje zadat **zásady streamování**. **Zásady streamování** umožňují definovat protokoly streamování a možnosti šifrování (pokud existuje) pro **lokátory streamování**. Dynamické balení se používá bez ohledu na to, zda streamujete svůj obsah živě nebo na vyžádání. <br/><br/>**Dynamické manifesty** mediálních služeb můžete použít k vysílání datového proudu pouze určité interpretace nebo dílčích klipů videa.|[Dynamické balení](dynamic-packaging-overview.md)<br/><br/>[Koncové body streamování](streaming-endpoint-concept.md)<br/><br/>[Lokátory streamování](streaming-locators-concept.md)<br/><br/>[Zásady streamování](streaming-policy-concept.md)<br/><br/>[Dynamické manifesty](filters-dynamic-manifest-overview.md)<br/><br/>[Filtry](filters-concept.md)|
|Ochrana obsahu|Pomocí mediálních služeb můžete poskytovat obsah s živým i na vyžádání šifrovaný dynamicky pomocí advanced encryption standardu (AES-128) nebo/a některého ze tří hlavních systémů DRM: Microsoft PlayReady, Google Widevine a Apple FairPlay. Mediální služby také poskytují službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) oprávněným klientům. <br/><br/>Pokud v datovém proudu zadejte možnosti šifrování, vytvořte **zásady klíče obsahu** a přidružte je k **lokátoru streamování**. **Zásady klíče obsahu** umožňují nakonfigurovat způsob doručení klíče obsahu koncovým klientům.<br/><br/> Pokuste se znovu použít zásady vždy, když jsou potřeba stejné možnosti.| [Zásady symetrických klíčů](content-key-policy-concept.md)<br/><br/>[Ochrana obsahu](content-protection-overview.md)|
|Živé streamování|Mediální služby vám umožňují doručovat živé události zákazníkům v cloudu Azure. **Živé události** zodpovídají za ingestování a zpracování informačních kanálů živého videa. Při vytváření **živé události**je vytvořen vstupní koncový bod, který můžete použít k odeslání živého signálu ze vzdáleného kodéru. Jakmile budete mít datový proud proudící do **živé události**, můžete zahájit streamovací událost vytvořením **datového zdroje**, **živého výstupu**a **lokátoru streamování**. **Živý výstup** archivuje datový proud do **datového zdroje** a zpřístupní jej divákům prostřednictvím **koncového bodu streamování**. Živou událost lze nastavit buď na *předávací* (místní živý kodér odešle datový proud s více datovým tokem) nebo *živé kódování* (místní živý kodér odešle jeden datový proud datového toku). |[Přehled živého streamování](live-streaming-overview.md)<br/><br/>[Živé události a výstupy](live-events-outputs-concept.md)|
|Monitorování pomocí mřížky událostí|Chcete-li zobrazit průběh úlohy, použijte **mřížku událostí**. Media Services také vyzařuje typy živých událostí. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. |[Zpracování událostí služby Event Grid](reacting-to-media-services-events.md)<br/><br/>[Schémata](media-services-event-schemas.md)|
|Monitorování pomocí Azure Monitoru|Sledujte metriky a diagnostické protokoly, které vám pomůžou pochopit, jak si vaše aplikace vedou pomocí Azure Monitoru.|[Metriky a diagnostické protokoly](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schémata diagnostických protokolů](media-services-diagnostic-logs-schema.md)|
|Klienti přehrávače|Azure Media Player můžete použít k přehrávání mediálního obsahu streamovaného službou Media Services v celé řadě prohlížečů a zařízení. Azure Media Player používá oborové standardy, jako je HTML5, rozšíření zdroje médií (MSE) a rozšíření šifrovaných médií (EME) k poskytování obohaceného adaptivního streamování. |[Přehled Azure Media Playeru](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

* [Kódování vzdáleného souboru a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kódování načteného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 – .NET](protect-with-aes128.md)
* [Šifrovat dynamicky pomocí multi-DRM - .NET](protect-with-drm.md)
