---
title: Azure Media Services terminologie a koncepce – Azure | Dokumentace Microsoftu
description: Toto téma nabízí stručný přehled služby Azure Media Services terminologie a koncepty a obsahuje odkazy na další podrobnosti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1e76569c7f5157dce681d15ec8d499b90e080102
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762313"
---
# <a name="media-services-concepts"></a>Koncepty služby Media Services

Toto téma nabízí stručný přehled Azure Media Services terminologie a koncepty. Článek taky obsahuje odkazy na články s podrobnější vysvětlení Media Services v3 koncepty a funkce. 

Základní koncepty popsané v těchto témat byste měli zkontrolovat před spuštěním vývoje.

> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Terminologie

Tato část ukazuje, jak se některé běžné oborových termínech mapují na rozhraní API služby Media Services v3.

### <a name="live-event"></a>Živá událost

A **živá událost** představuje kanál pro příjem, překódování (volitelně) a balení živé streamy metadat videa, zvuku a v reálném čase.

Pro zákazníky, kteří migrace z rozhraní API služby Media Services v2 **živá událost** nahradí **kanál** entit ve verzi 2. Další informace najdete v tématu [migrace z v2 na v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Koncový bod streamování (balení a původu)

A **koncový bod streamování** představuje dynamické (just-in-time) obalu a původu službu, která může doručovat obsah na vyžádání a živé přímo do klientské aplikace přehrávače, jedním z běžných streamovacích protokolů média (HLS nebo DASH). Kromě toho **koncový bod streamování** poskytuje dynamického šifrování (just-in-time) na špičkové technologiemi DRM.

Na médiu streamování odvětví, tato služba se obvykle označuje jako **Packager** nebo **původu**.  Další běžné podmínky v oboru pro tuto funkci zahrnují JITP (Just v – čas packager) nebo JITE (Just v – čas šifrování). 
 
## <a name="cloud-upload-and-storage"></a>Nahrávání na cloud a úložiště

Začít spravovat, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services a do digitální soubory nahrát **prostředky**.

- [Nahrávání do cloudu a úložiště](storage-account-concept.md)
- [Koncept prostředky](assets-concept.md)

## <a name="encoding"></a>Kódování

Jakmile nahrajete do prostředky vysoce kvalitní digitální mediální soubory, zakódovat je do formátů, které můžete přehrát na širokou škálu prohlížečů a zařízení. 

Ke kódování pomocí Media Services v3, je potřeba vytvořit **transformuje** a **úlohy**.

![Transformace](./media/encoding/transforms-jobs.png)

- [Transformace a úlohy](transforms-jobs-concept.md)
- [Kódování pomocí Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Analýzy mediálních služeb

K analýze videí a zvukových souborů, je také potřeba vytvořit **transformuje** a **úlohy**.

- [Analýza videosouborů a zvukových souborů](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Balení, doručování, ochrana

Jakmile je váš obsah kódovaný, můžete využít výhod **dynamické balení**. Ve službě Media Services **koncový bod streamování**  /původ dynamické balení služby využívat k poskytování mediálního obsahu pro klientské přehrávače. Chcete-li zpřístupnit videa v prostředku výstupu pro klienty pro přehrávání, budete muset vytvořit **Lokátor streamování** a následně vytvořit adresy URL pro streamování. 

Při vytváření **Lokátor streamování**, kromě název assetu, musíte zadat **streamování zásad**. **Streamování zásady** vám umožňují definovat streamovacích protokolů a šifrování možnosti (pokud existuje) pro vaše **lokátory streamování**.

Dynamické balení se používá, zda streamování vašeho obsahu, živě nebo na vyžádání. Následující diagram znázorňuje streamování na vyžádání s dynamickým vytvářením paketů pracovního postupu.

![Dynamické balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Pomocí služby Media Services můžete doručovat na vyžádání a živého obsahu šifrován dynamicky Advanced Encryption Standard (AES-128) nebo / a systémů tři hlavní digitálních práv management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

Při zadání možnosti šifrování na datový proud, vytvořte **obsahu zásad klíče** a přidružte jej k vaší **Lokátor streamování**. **Zásad klíče k obsahu** vám umožní nakonfigurovat, jak je klíč k obsahu doručit koncovým klientům.

Následující obrázek ukazuje pracovní postup služby Media Services content protection: 

![Ochrana obsahu](./media/content-protection/content-protection.svg)

&#42;dynamické šifrování podporuje AES-128 "nezašifrovaný klíč", CBCS a CENC. 

Služba Media Services můžete použít **dynamických manifestů** Streamovat pouze konkrétní verze nebo subclips vašeho videa. V následujícím příkladu byl použit pro kodér má kódovat mezzanine asset do sedmi interpretace video soubory MP4 rychlostmi ISO (z 180p 1080p). Zakódovanému assetu můžete dynamicky zabalené do některé z následujících protokolů streamování: HLS, MPEG DASH a Smooth.  V horní části diagramu se zobrazí HLS manifestu pro prostředek s žádné filtry (obsahuje všechny sedm interpretace).  Vlevo dole se zobrazí v manifestu HLS, do které byl použit filtr s názvem "ott". Filtr "ott" Určuje odebrání všech přenosových rychlostí nižší než 1 MB/s, což způsobilo v dolní části dvě úrovně kvality se odstraní v odpovědi. V pravém dolním rohu se zobrazí v manifestu HLS, do které byl použit filtr s názvem "mobilní". "Mobilní" filtr určuje odebrání interpretací, kde je větší než 720p, což způsobilo ve dvou rozlišení 1080p interpretace se odstraní.

![Interpretace filtrování](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamické balení](dynamic-packaging-overview.md)
- [Koncové body streamování](streaming-endpoint-concept.md)
- [Lokátory streamování](streaming-locators-concept.md)
- [Zásady streamování](streaming-policy-concept.md)
- [Zásady symetrických klíčů](content-key-policy-concept.md)
- [Ochrana obsahu](content-protection-overview.md)
- [Dynamické manifesty](filters-dynamic-manifest-overview.md)
- [Filtry](filters-concept.md)

## <a name="live-streaming"></a>Živé streamování

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. **Živé události** zodpovídají za ingestování a zpracování informačních kanálů živého videa. Když vytvoříte **živá událost**, vstupní koncový bod se vytvoří, můžete použít k odesílání živě signál z vzdálený kodér. Jakmile máte datový proud plyne do **živá událost**, streamování událostí můžete začít tak, že vytvoříte **Asset**, **Live výstup**, a **Lokátor streamování** . **Live výstup** bude archivovat do datového proudu **Asset** a zpřístupní se divákům prostřednictvím **koncový bod streamování**. A **živá událost** může být jeden ze dvou typů: **předávací** a **živého kódování**.

Následující obrázek ukazuje pracovní postup předávací typu:

![Průchozí](./media/live-streaming/pass-through.svg)

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé události a výstupy](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitorování

### <a name="event-grid"></a>Event Grid

Chcete-li zobrazit průběh úlohy, měli byste použít **služby Event Grid**. Služba Media Services také vysílá typy živé události. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. 

- [Zpracování událostí služby Event Grid](reacting-to-media-services-events.md)
- [Schémata](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Monitorování metrik a diagnostické protokoly, které vám pomohou pochopit, jak aplikace fungují s Azure Monitor.

- [Metriky a diagnostické protokoly](media-services-metrics-diagnostic-logs.md)
- [Schémata pro diagnostické protokoly](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Klienti přehrávače

Azure Media Player můžete použít k přehrávání mediálního obsahu, které streamují Media Services na širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroj média (MSE) a rozšíření eme (Encrypted Media Extensions), k poskytování bohatších možností adaptivního streamování. 

- [Přehled Azure Media Playeru](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

* [Kódování vzdáleného souboru a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kódování nahraný soubor a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Stream živě – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 - .NET](protect-with-aes128.md)
* [Dynamicky šifrovat pomocí několika variant DRM – .NET](protect-with-drm.md) 
