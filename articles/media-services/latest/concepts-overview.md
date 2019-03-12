---
title: Principy Azure Media Services – Azure | Dokumentace Microsoftu
description: Toto téma nabízí stručný přehled o konceptech Azure Media Services a obsahuje odkazy na podrobnosti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b346ac0ac456b7f6af078496e8dd1cb738caab67
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530190"
---
# <a name="media-services-concepts"></a>Koncepty služby Media Services

Toto téma nabízí stručný přehled o konceptech Azure Media Services a obsahuje odkazy na články s podrobnější vysvětlení Media Services v3 koncepty a funkce. Základní koncepty popsané v těchto témat byste měli zkontrolovat před spuštěním vývoje.

## <a name="cloud-upload-and-storage"></a>Nahrávání na cloud a úložiště

Začít spravovat, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services a do digitální soubory nahrát **prostředky**.

- [Nahrávání na cloud a úložiště](storage-account-concept.md)
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

Jakmile je váš obsah kódovaný, můžete využít výhod **dynamické balení**. **Koncový bod streamování** je služba dynamického balení ve službě Media Services používá k doručování multimediálního obsahu pro klientské přehrávače. Chcete-li zpřístupnit videa v prostředku výstupu pro klienty pro přehrávání, budete muset vytvořit **Lokátor streamování** a následně vytvořit adresy URL pro streamování. 

Při vytváření **Lokátor streamování**, kromě název assetu, musíte zadat **streamování zásad**. **Streamování zásady** vám umožňují definovat streamovacích protokolů a šifrování možnosti (pokud existuje) pro vaše **lokátory streamování**.

Dynamické balení se používá, zda streamování vašeho obsahu, živě nebo na vyžádání. Následující diagram znázorňuje streamování na vyžádání s dynamickým vytvářením paketů pracovního postupu.

![Dynamické balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

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
- [Streamování zásady](streaming-policy-concept.md)
- [Zásady klíče obsahu](content-key-policy-concept.md)
- [Ochrana obsahu](content-protection-overview.md)
- [Dynamických manifestů](filters-dynamic-manifest-overview.md)
- [Filtry](filters-concept.md)

## <a name="live-streaming"></a>Živé streamování

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. **Živé události** zodpovídají za příjem a zpracování živého videa informačních kanálů. Když vytvoříte **živá událost**, vstupní koncový bod se vytvoří, můžete použít k odesílání živě signál z vzdálený kodér. Jakmile máte datový proud plyne do **živá událost**, streamování událostí můžete začít tak, že vytvoříte **Asset**, **Live výstup**, a **Lokátor streamování** . **Live výstup** bude archivovat do datového proudu **Asset** a zpřístupní se divákům prostřednictvím **streamování Endpoid**. A **živá událost** může být jeden ze dvou typů: **předávací** a **živého kódování**.

Následující obrázek ukazuje pracovní postup předávací typu:

![Předávací](./media/live-streaming/pass-through.svg)

- [Přehled živého streamování](live-streaming-overview.md)
- [Události v reálném čase a živé výstupy](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitorování

### <a name="event-grid"></a>Event Grid

Chcete-li zobrazit průběh úlohy, měli byste použít **služby Event Grid**. Služba Media Services také vysílá typy živé události. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. 

- [Zpracování událostí služby Event Grid](reacting-to-media-services-events.md)
- [Schémata](media-services-event-schemas.md)

## <a name="player-clients"></a>Klienti přehrávače

Azure Media Player můžete použít k přehrávání mediálního obsahu, které streamují Media Services na širokou škálu prohlížečů a zařízení. Azure Media Player využívá oborové standardy, jako je HTML5, rozšíření zdroj média (MSE) a rozšíření eme (Encrypted Media Extensions), k poskytování bohatších možností adaptivního streamování. 

- [Přehled služby Azure Media Player](use-azure-media-player.md)

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování a streamování pomocí služby Media Services](stream-files-tutorial-with-api.md)
