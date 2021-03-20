---
title: Media Services terminologie a koncepty
description: Seznamte se s terminologie a koncepty pro Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 309ef8baada22f59b1395164626dd664366cb4d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581129"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services terminologie a koncepty

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Toto téma poskytuje stručný přehled Azure Media Services terminologie a koncepce. Článek také obsahuje odkazy na články s podrobným vysvětlením konceptů a funkcí Media Services V3.

Základní koncepty popsané v těchto tématech by měly být před zahájením vývoje přezkoumány.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologie Media Services V3

|Období|Description|
|---|---|
|Živá událost|**Živá událost** představuje kanál pro ingestování, překódování (volitelně) a balení živých streamů pro video, zvuk a metadata v reálném čase.<br/><br/>Pro zákazníky, kteří migrují z rozhraní API Media Services V2, nahradí **živá událost** entitu **kanálu** ve verzi v2. Další informace najdete v tématu [migrace z verze V2 na V3](migrate-v-2-v-3-migration-introduction.md).|
|Koncový bod streamování/balení/počátek|**Koncový bod streamování** představuje dynamický (just-in-time) balení a službu původu, která může doručovat obsah živě a na vyžádání přímo do aplikace klienta v přehrávači. Používá jeden z běžných protokolů mediálního streamování (HLS nebo POMLČKy). **Koncový bod streamování** navíc poskytuje dynamické šifrování (za běhu) k špičkovým systémům pro správu digitálních práv (několikanásobnou) v oboru.<br/><br/>V odvětví streamování médií se tato služba obvykle označuje jako **balírna** nebo **původ**.  Mezi další běžné výrazy v odvětví této funkce patří JITP (just-in-time-Package) nebo JITE (za běhu).

## <a name="media-services-v3-concepts"></a>Koncepty Media Services V3

|Koncepty|Description|Odkazy|
|---|---|---|
|Prostředky a nahrávání obsahu|Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services a nahrát své digitální soubory do **assetů**.|[Nahrávání do cloudu a úložiště](storage-account-concept.md)<br/><br/>[Koncept prostředků](assets-concept.md)|
|Kódování obsahu|Po nahrání vysoce kvalitních digitálních mediálních souborů do assetů je můžete kódovat do formátů, které se dají přehrávat na nejrůznějších prohlížečích a zařízeních. <br/><br/>Pro kódování pomocí Media Services V3 potřebujete vytvořit **transformace** a **úlohy**.|[Transformace a úlohy](transforms-jobs-concept.md)<br/><br/>[Kódování pomocí Media Services](encoding-concept.md)|
|Analýza obsahu (Video Indexer)|Media Services V3 umožňuje z vašich videí a zvukových souborů extrahovat přehledy pomocí přednastavených Media Services V3. Chcete-li analyzovat obsah pomocí přednastavených Media Services V3, je nutné vytvořit **transformace** a **úlohy**.<br/><br/>Pokud potřebujete podrobnější přehled, použijte [video indexer](../video-indexer/index.yml) přímo.|[Analýza videosouborů a zvukových souborů](analyzing-video-audio-files-concept.md)|
|Balení a doručování|Po zakódování obsahu můžete využít výhod **dynamického balení**. V Media Services **koncový bod streamování** je dynamická služba balení, která se používá k doručování mediálního obsahu klientským přehrávačům. Chcete-li zpřístupnit video výstupnímu prostředku pro klienty pro přehrávání, je nutné vytvořit **Lokátor streamování** a potom sestavit adresy URL streamování. <br/><br/>Při vytváření **lokátoru streamování** se kromě názvu assetu musí zadat **zásada streamování**. **Zásady streamování** umožňují definovat protokoly streamování a možnosti šifrování (pokud existují) pro vaše **Lokátory streamování**. Dynamické balení se používá bez ohledu na to, jestli Streamujte svůj obsah v reálném čase nebo na vyžádání. <br/><br/>Můžete použít Media Services **dynamické manifesty** pro streamování pouze konkrétní verze nebo dílčích klipů vašeho videa.|[Dynamické balení](dynamic-packaging-overview.md)<br/><br/>[Koncové body streamování](streaming-endpoint-concept.md)<br/><br/>[Lokátory streamování](streaming-locators-concept.md)<br/><br/>[Zásady streamování](streaming-policy-concept.md)<br/><br/>[Dynamické manifesty](filters-dynamic-manifest-overview.md)<br/><br/>[Filtry](filters-concept.md)|
|Ochrana obsahu|Pomocí Media Services můžete zajistit dynamické šifrování obsahu v reálném čase a na vyžádání pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo/a kteréhokoliv ze tří hlavních systémů DRM: Microsoft PlayReady, Google Widevine a Apple FairPlay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) autorizovaným klientům. <br/><br/>Pokud pro svůj Stream určíte možnosti šifrování, vytvořte **zásady klíčů obsahu** a přidružte je k **lokátoru streamování**. **Zásady klíčů obsahu** umožňují konfigurovat způsob doručení klíče obsahu koncovým klientům.<br/><br/> Zkuste znovu použít zásady, pokud jsou potřeba stejné možnosti.| [Zásady pro klíč obsahu](content-key-policy-concept.md)<br/><br/>[Ochrana obsahu](content-protection-overview.md)|
|Živé streamování|Media Services vám umožní doručovat živé události zákazníkům v cloudu Azure. **Živé události** zodpovídají za ingestování a zpracování informačních kanálů živého videa. Když vytvoříte **živou událost**, vytvoří se vstupní koncový bod, který můžete použít k odeslání živého signálu ze vzdáleného kodéru. Jakmile datový proud přetéká do **živé události**, můžete zahájit streamování událostí vytvořením **assetu**, **živého výstupu** a **lokátoru streamování**. **Live Output** archivuje datový proud do **assetu** a zpřístupní ho uživatelům prostřednictvím **koncového bodu streamování**. Živá událost může být nastavená na *předávací* (místní živý kodér posílá datový proud s více přenosovými rychlostmi) nebo *živé kódování* (místní kodér Live Encoder posílá datový proud s jednou přenosovou rychlostí). |[Přehled živého streamování](live-streaming-overview.md)<br/><br/>[Živé události a výstupy](live-events-outputs-concept.md)|
|Monitorování pomocí Event Grid|Chcete-li zobrazit průběh úlohy, použijte **Event Grid**. Media Services také emituje typy událostí typu Live. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. |[Zpracování událostí služby Event Grid](monitoring/reacting-to-media-services-events.md)<br/><br/>[Schémata](monitoring/media-services-event-schemas.md)|
|Monitorování pomocí Azure Monitor|Sledujte metriky a diagnostické protokoly, které vám pomůžou pochopit, jak aplikace pracují s Azure Monitor.|[Metriky a diagnostické protokoly](monitoring/monitor-media-services-data-reference.md)<br/><br/>[Schémata diagnostických protokolů](monitoring/monitor-media-services-data-reference.md)|
|Klienti přehrávače|Azure Media Player můžete použít k přehrání multimediálního obsahu streamování Media Services na nejrůznějších prohlížečích a zařízeních. Azure Media Player využívá oborové standardy, jako je HTML5, Media source Extensions (MSE) a rozšíření EME (Encrypted Media Extensions), a poskytuje tak obohacené možnosti adaptivního streamování. |[Přehled Azure Media Playeru](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

* [Kódování vzdáleného souboru a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kódování načteného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 – .NET](protect-with-aes128.md)
* [Dynamické šifrování pomocí více DRM – technologie .NET](protect-with-drm.md)