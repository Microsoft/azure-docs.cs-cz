---
title: Azure Media Services terminologie a koncepty – Azure | Microsoft Docs
description: Toto téma poskytuje stručný přehled Azure Media Services terminologie a koncepce a poskytuje odkazy pro další podrobnosti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 39bdcc94b785371044b5d49fd844a06a176a8fba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970032"
---
# <a name="media-services-concepts"></a>Media Services koncepty

Toto téma poskytuje stručný přehled Azure Media Services terminologie a koncepce. Článek také obsahuje odkazy na články s podrobným vysvětlením konceptů a funkcí Media Services V3. 

Základní koncepty popsané v těchto tématech by měly být před zahájením vývoje přezkoumány.

> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Terminologie

V této části se dozvíte, jak se některé běžné obory mapují na rozhraní Media Services V3 API.

### <a name="live-event"></a>Živá akce

**Živá událost** představuje kanál pro ingestování, překódování (volitelně) a balení živých streamů pro video, zvuk a metadata v reálném čase.

Pro zákazníky, kteří migrují z rozhraní API Media Services V2, nahradí **živá událost** entitu **kanálu** ve verzi v2. Další informace najdete v tématu [migrace z verze V2 na V3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Koncový bod streamování (balení a původ)

**Koncový bod streamování** představuje dynamický (za běhu) balení a službu Origin Service, která může doručovat obsah živě a na vyžádání přímo do klientské aplikace přehrávače pomocí jednoho z běžných protokolů mediálního streamování (HLS nebo pomlčky). **Koncový bod streamování** navíc zajišťuje dynamické šifrování (za běhu) pro špičkové několikanásobnou.

V odvětví streamování médií se tato služba obvykle označuje jako **balírna** nebo **původ**.  Mezi další běžné výrazy v odvětví této funkce patří JITP (just-in-time-Package) nebo JITE (za běhu). 
 
## <a name="cloud-upload-and-storage"></a>Nahrávání na cloud a úložiště

Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services a nahrát své digitální soubory do **assetů**.

- [Nahrávání do cloudu a úložiště](storage-account-concept.md)
- [Koncept prostředků](assets-concept.md)

## <a name="encoding"></a>Encoding

Po nahrání vysoce kvalitních digitálních mediálních souborů do assetů je můžete kódovat do formátů, které se dají přehrávat na nejrůznějších prohlížečích a zařízeních. 

Pro kódování pomocí Media Services V3 potřebujete vytvořit **transformace** a **úlohy**.

![Transformace](./media/encoding/transforms-jobs.png)

- [Transformace a úlohy](transforms-jobs-concept.md)
- [Kódování pomocí Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Analýza médií

Chcete-li analyzovat videosoubory a zvukové soubory, musíte také vytvořit **transformace** a **úlohy**.

- [Analýza videosouborů a zvukových souborů](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Balení, doručování, ochrana

Po zakódování obsahu můžete využít výhod **dynamického balení**. V Media Services je/Origin **koncový bod streamování**, který se používá k doručování mediálního obsahu do klientských hráčů. Chcete-li zpřístupnit video výstupnímu prostředku pro klienty pro přehrávání, je nutné vytvořit **Lokátor streamování** a potom sestavit adresy URL streamování. 

Při vytváření **lokátoru streamování**se kromě názvu prostředku musí zadat **zásada streamování**. **Zásady streamování** umožňují definovat protokoly streamování a možnosti šifrování (pokud existují) pro vaše **Lokátory streamování**.

Dynamické balení se používá bez ohledu na to, jestli Streamujte svůj obsah v reálném čase nebo na vyžádání. Následující diagram znázorňuje streamování na vyžádání s dynamickým pracovním postupem pro balení.

![Dynamické balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Pomocí Media Services můžete zajistit dynamické šifrování obsahu v reálném čase a na vyžádání s využitím standard AES (Advanced Encryption Standard) (AES-128) nebo/a kteréhokoli ze tří hlavních systémů správy digitálních práv (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

Pokud pro svůj Stream určíte možnosti šifrování, vytvořte **zásady klíčů obsahu** a přidružte je k **lokátoru streamování**. **Zásady klíčů obsahu** umožňují konfigurovat způsob doručení klíče obsahu koncovým klientům.

Následující obrázek ukazuje pracovní postup služby Media Services content protection: 

![Ochrana obsahu](./media/content-protection/content-protection.svg)

&#42;dynamické šifrování podporuje AES-128 "Clear Key", CBCS a CENC. 

Můžete použít Media Services **dynamické manifesty** pro streamování pouze konkrétní verze nebo dílčích klipů vašeho videa. V následujícím příkladu byl kodér použit ke kódování Mezzanine prostředku do sedmi verzí rychlostmi videa ISO (od 180p do 1080p). Kódovaný prostředek se dá dynamicky balit do libovolného z následujících protokolů streamování: HLS, MPEG POMLČKa a hladký.  V horní části diagramu se zobrazí manifest HLS pro Asset bez filtrů (obsahuje všechny sedm verzí).  V levém dolním rohu se zobrazí manifest HLS, na který se použil filtr s názvem "OTT". Filtr "OTT" Určuje, že se mají odebrat všechny přenosové rychlosti pod 1 MB/s, což vedlo k tomu, že se v odpovědi odeberou poslední dvě úrovně kvality. V pravém dolním rohu se zobrazí manifest HLS, na který se použil filtr s názvem "mobilní". Filtr "mobilní" Určuje, že se mají odebrat verze, kde je rozlišení větší než 720p, což vede k vypínání dvou verzí 1080p.

![Filtrování verzí](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Dynamické balení](dynamic-packaging-overview.md)
- [Koncové body streamování](streaming-endpoint-concept.md)
- [Lokátory streamování](streaming-locators-concept.md)
- [Zásady streamování](streaming-policy-concept.md)
- [Zásady symetrických klíčů](content-key-policy-concept.md)
- [Ochrana obsahu](content-protection-overview.md)
- [Dynamické manifesty](filters-dynamic-manifest-overview.md)
- [Filtry](filters-concept.md)

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="live-streaming"></a>Živé streamování

Azure Media Services vám umožní doručovat živé události zákazníkům v cloudu Azure. **Živé události** zodpovídají za ingestování a zpracování informačních kanálů živého videa. Když vytvoříte **živou událost**, vytvoří se vstupní koncový bod, který můžete použít k odeslání živého signálu ze vzdáleného kodéru. Jakmile datový proud přetéká do **živé události**, můžete zahájit streamování událostí vytvořením **assetu**, **živého výstupu**a **lokátoru streamování**. **Live Output** archivuje datový proud do **assetu** a zpřístupní ho uživatelům prostřednictvím **koncového bodu streamování**. **Živá událost** může být jeden ze dvou typů: **průchozí** a **živé kódování**.

Následující obrázek znázorňuje pracovní postup předávacího typu:

![Průchozí](./media/live-streaming/pass-through.svg)

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé události a výstupy](live-events-outputs-concept.md)

## <a name="monitoring"></a>Sledování

### <a name="event-grid"></a>Event Grid

Chcete-li zobrazit průběh úlohy, měli byste použít **Event Grid**. Media Services také emituje typy událostí typu Live. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pochází v podstatě z jakékoli služby Azure nebo vlastních zdrojů. 

- [Zpracování událostí Event Grid](reacting-to-media-services-events.md)
- [Schémata](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Sledujte metriky a diagnostické protokoly, které vám pomohou pochopit, jak aplikace pracují s Azure Monitor.

- [Metriky a diagnostické protokoly](media-services-metrics-diagnostic-logs.md)
- [Schémata protokolů diagnostiky](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Klienti přehrávače

Azure Media Player můžete použít k přehrání multimediálního obsahu streamování Media Services na nejrůznějších prohlížečích a zařízeních. Azure Media Player využívá oborové standardy, jako je HTML5, Media source Extensions (MSE) a rozšíření EME (Encrypted Media Extensions), aby poskytovala obohacené možnosti adaptivního streamování. 

- [Přehled Azure Media Playeru](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

* [Zakódovat vzdálené souborové video a streamování – REST](stream-files-tutorial-with-rest.md)
* [Kódování nahraného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 – .NET](protect-with-aes128.md)
* [Dynamické šifrování pomocí více DRM – technologie .NET](protect-with-drm.md) 
