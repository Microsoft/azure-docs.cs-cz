---
title: Scénáře využití služby Microsoft Azure Media Services a dostupnost funkcí v datových centrech | Microsoft Docs
description: V tomto tématu najdete přehled scénářů využití služby Microsoft Azure Media Services a dostupnost funkcí a služeb v datových centrech.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 777ba32a6c3ec7545c9911c820ceda7ac823096a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Scénáře a dostupnost funkcí služby Media Services v datových centrech

Microsoft Azure Media Services (AMS) umožňuje bezpečně nahrávat, ukládat, kódovat a balit obsah (video nebo zvuk) doručovaný na vyžádání i živě streamovaný různým klientům (například do televizí, počítačů a mobilních zařízení).

AMS funguje v několika datových centrech po celém světě. Tato datová centra jsou seskupená do geografických oblastí. To vám poskytuje flexibilitu při výběru místa pro sestavení vašich aplikací. [Seznam oblastí a jejich umístění](https://azure.microsoft.com/regions/) si můžete prohlédnout. 

Toto téma představuje běžné scénáře doručování obsahu [živě](#live_scenarios) nebo [na vyžádání](#vod_scenarios). V tématu najdete také podrobnosti o dostupnosti funkcí a služeb pro média v datových centrech.

## <a name="overview"></a>Přehled

### <a name="prerequisites"></a>Požadavky

Pokud chcete začít používat Azure Media Services, potřebujete následující:

* Účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com).
* Účet Azure Media Services. Další informace najdete v článku o [vytvoření účtu](media-services-portal-create-account.md).
* Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**.

    Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování být ve stavu **Spuštěno**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Běžně používané objekty při vývoji na základě modelu AMS OData

Následující obrázek ukazuje některé z nejčastěji používaných objektů při vývoji na základě modelu Media Services OData.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Celý model můžete zobrazit [zde](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Ochrana obsahu v úložišti a doručování streamovaných médií v nešifrované podobě

![Pracovní postup videa na vyžádání (VoD)](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Nahrajte do prostředku vysoce kvalitní mediální soubor.

    Doporučujeme použít na asset možnost šifrování úložiště. Ochráníte tak svůj obsah během nahrávání a během jeho pobytu v úložišti.
2. Zakódujte ho do sady souborů MP4 s adaptivní přenosovou rychlostí.

    Na výstupní asset doporučujeme použít možnost šifrování úložiště. Ochráníte tak váš obsah v úložišti.
3. Nakonfigurujte zásady doručení assetu (používané dynamickým balením).

    Pokud váš asset používá šifrování úložiště, **musíte** nakonfigurovat zásady doručení assetu.
4. Publikujte asset tím, že vytvoříte lokátor OnDemand.
5. Streamujte publikovaný obsah.

Informace o dostupnosti v datových centrech najdete v části [Dostupnost](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Ochrana obsahu v úložišti, doručování dynamicky šifrovaných streamovaných médií

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Nahrajte do prostředku vysoce kvalitní mediální soubor. Na asset použijte možnost šifrování úložiště.
2. Zakódujte ho do sady souborů MP4 s adaptivní přenosovou rychlostí. Na výstupní asset použijte možnost šifrování úložiště.
3. Pokud chcete asset během přehrávání dynamicky šifrovat, vytvořte šifrovací klíč obsahu.
4. Nakonfigurujte zásady autorizace klíče obsahu.
5. Nakonfigurujte zásady doručení assetu (používané dynamickým balením a dynamickým šifrováním).
6. Publikujte asset tím, že vytvoříte lokátor OnDemand.
7. Streamujte publikovaný obsah.

Informace o dostupnosti v datových centrech najdete v části [Dostupnost](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Použití Media Analytics k získání prakticky uplatnitelných informací z videí

Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. Další informace najdete v článku o [přehledu Azure Media Services Analytics](media-services-analytics-overview.md).

1. Nahrajte do prostředku vysoce kvalitní mediální soubor.
2. Zpracovávejte videa pomocí některé ze služeb Analýzy mediálních služeb popsaných v části [Přehled Analýz mediálních služeb](media-services-analytics-overview.md).
3. Procesory médií z Media Analytics vytvářejí soubory MP4 nebo soubory JSON. Pokud procesor médií vytvořil soubor MP4, můžete ho progresivně stahovat. Pokud procesor médií vytvořil soubor JSON, můžete ho stáhnout z úložiště objektů blob v Azure.

Informace o dostupnosti v datových centrech najdete v části [Dostupnost](#availability).

## <a name="deliver-progressive-download"></a>Doručení progresivního stahování

1. Nahrajte do prostředku vysoce kvalitní mediální soubor.
2. Zakódujte ho do jednoho souboru MP4.
3. Publikujte asset tím, že vytvoříte lokátor OnDemand nebo SAS.

    Pokud používáte lokátor SAS, bude se obsah stahovat z úložiště objektů blob v Azure. V takovém případě nepotřebujete koncové body streamování ve spuštěném stavu.
4. Progresivně stáhněte obsah.

## <a id="live_scenarios"></a>Doručování živě streamovaných událostí 

1. Ingestujte živý obsah pomocí různých protokolů pro živé streamování (například RTMP nebo technologie Smooth Streaming).
2. (volitelně) Kódujte datový proud na datový proud s adaptivní přenosovou rychlostí.
3. Zobrazte náhled živého streamování.
4. Doručujte obsah prostřednictvím běžných streamovacích protokolů (například MPEG DASH, Smooth, HLS) přímo k vašim zákazníkům nebo do sítě pro doručování obsahu (CDN) pro další distribuci.

    -nebo-

    Zaznamenávejte a ukládejte ingestovaný obsah, abyste ho mohli streamovat později (video na vyžádání).

Při živém streamování můžete zvolit jednu z následujících cest:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů (průchozí)

Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na **průchozím** pracovním postupu.

![Živý pracovní postup](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Další informace najdete v článku o [práci s kanály, které přijímají živé streamování s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Práce s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services

Následující diagram znázorňuje hlavní část platformy AMS, které se podílejí na pracovním postupu živého streamování, ve kterém má kanál povolené kódování v reálném čase pomocí služby Media Services.

![Živý pracovní postup](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Informace o dostupnosti v datových centrech najdete v části [Dostupnost](#availability).

## <a name="consuming-content"></a>Konzumace obsahu

Služba Azure Media Services nabízí nástroje, které potřebujete k vytvoření dynamických aplikací pro klientské přehrávače pro většinu platforem včetně: zařízení iOS, zařízení Android, Windows, Windows Phone, Xbox a set top boxy. Následující téma obsahuje odkazy na sady SDK a architektury přehrávačů, které můžete použít pro vývoj vlastních klientských aplikací, které můžou využívat streamovaná média ze služby Media Services. Další informace najdete v tématu [Vývoj aplikací pro přehrávání videa](media-services-develop-video-players.md).

## <a name="enabling-azure-cdn"></a>Povolení Azure CDN

Služba Media Services podporuje integraci s Azure CDN. Informace o povolení Azure CDN najdete v článku o [správě koncových bodů streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a id="scaling"></a>Škálování účtu Media Services

Zákazníci AMS můžou ve svých účtech AMS škálovat koncové body streamování, zpracování médií a úložiště.

* Zákazníci Media Services si můžou zvolit koncový bod streamování **Standard**, nebo koncový bod streamování **Premium**. Koncový bod streamování **Standard** je vhodný pro většinu úloh streamování. Zahrnuje stejné funkce jako koncové body streamování **Premium** a automaticky škáluje šířku odchozího pásma. 

    Koncové body streamování **Premium** jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Zákazníci, kteří mají koncový bod streamování **Premium**, ve výchozím nastavení získají jednu jednotku streamování (SU). Koncový bod streamování je možné škálovat přidáním jednotek streamování. Každá jednotka streamování poskytuje aplikaci další kapacitu šířky pásma. Další informace o škálování koncových bodů streamování **Premium** najdete v tématu [Škálování koncových bodů streamování](media-services-portal-scale-streaming-endpoints.md).

* Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**.

    Kromě určení typu rezervované jednotky můžete určit, že chcete účet zřídit s **rezervovanými jednotkami** (RU). Počet zřízených RU určuje počet úloh médií, které je možné v daném účtu zpracovávat současně.

    >[!NOTE]
    >RU fungují pro paralelní provádění veškerého zpracování médií, včetně úloh indexování pomocí Azure Media Indexeru. Ale na rozdíl od kódování se úlohy indexování s rychlejšími rezervovanými jednotkami nezpracovávají rychleji.

    Další informace najdete v tématu [Škálování zpracování médií](media-services-portal-scale-media-processing.md).
* Svůj účet Media Services můžete škálovat také tím, že k němu přidáte účty úložiště. Každý účet úložiště je omezen na 500 TB. Pokud chcete úložiště rozšířit nad jeho výchozí omezení, můžete k jednomu účtu Media Services připojit více účtů úložiště. Další informace najdete v tématu [Správa účtů úložiště](meda-services-managing-multiple-storage-accounts.md).

##<a id="availability"></a>Dostupnost funkcí služby Media Services v datových centrech

V této části najdete podrobnosti o dostupnosti funkcí služby Media Services v datových centrech.

### <a name="ams-accounts"></a>Účty AMS

#### <a name="availability"></a>Dostupnost

Pokud chcete zjistit, jestli je v datovém centru k dispozici služba Media Services, přejděte na https://azure.microsoft.com/status/ a posuňte se na tabulku MÉDIA.

### <a name="streaming-endpoints"></a>Koncové body streamování 

Zákazníci Media Services si můžou zvolit koncový bod streamování **Standard**, nebo koncový bod streamování **Premium**. Další informace najdete v části popisující [škálování](#scaling).

#### <a name="availability"></a>Dostupnost

|Název|Status|Datová centra
|---|---|---|
|Standard|GA|Vše|
|Premium|GA|Vše|

### <a name="live-encoding"></a>Kódování v reálném čase

#### <a name="availability"></a>Dostupnost

K dispozici ve všech datových centrech kromě oblastí: Německo, Brazílie – jih, Indie – západ, Indie – jih a Indie – střed. 

### <a name="encoding-media-processors"></a>Kódovací procesory médií

AMS nabízí dva kodéry na vyžádání – **Media Encoder Standard** a **Pracovní postup kodéru Media Encoder Premium**. Další informace najdete v tématu [Přehled a porovnání kodérů médií na vyžádání v Azure](media-services-encode-asset.md). 

#### <a name="availability"></a>Dostupnost

|Název procesoru médií|Status|Datová centra
|---|---|---|
|Media Encoder Standard|GA|Vše|
|Pracovní postup kodéru Media Encoder Premium|GA|Všechny s výjimkou Číny|

### <a name="analytics-media-processors"></a>Analytické procesory médií

Media Analytics je kolekce řečových a vizuálních komponent, které organizacím a podnikům umožňují, aby ze svých videosouborů odvodily prakticky využitelné informace. Další informace najdete v článku o [přehledu Azure Media Services Analytics](media-services-analytics-overview.md).

#### <a name="availability"></a>Dostupnost

|Název procesoru médií|Status|Datová centra
|---|---|---|
|Azure Media Face Detector|Preview|Vše|
|Azure Media Hyperlapse|Preview|Vše|
|Azure Media Indexer|GA|Vše|
|Azure Media Motion Detector|Preview|Vše|
|Azure Media OCR|Preview|Vše|
|Azure Media Redactor|Preview|Vše|
|Azure Media Stabilizer|Preview|Vše|
|Azure Media Video Thumbnails|Preview|Vše|
|Azure Media Indexer 2|Preview|Všechny s výjimkou Číny a oblasti federální vlády|

### <a name="protection"></a>Ochrana

Microsoft Azure Media Services umožňuje zabezpečení médií od okamžiku opuštění počítače přes uložení a zpracování až po doručení. Další informace najdete v tématu [Ochrana obsahu AMS](media-services-content-protection-overview.md).

#### <a name="availability"></a>Dostupnost

|Šifrování|Status|Datová centra|
|---|---|---| 
|Úložiště|GA|Vše|
|Klíče AES-128|GA|Vše|
|FairPlay|GA|Vše|
|PlayReady|GA|Vše|
|Widevine|GA|Všechna kromě oblastí Německo, Federální vláda a Čína.

### <a name="reserved-units-rus"></a>Rezervované jednotky (RU)

Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. 

Další informace najdete v části popisující [škálování](#scaling).

#### <a name="availability"></a>Dostupnost

K dispozici ve všech datových centrech.

### <a name="reserved-unit-ru-type"></a>Typ rezervované jednotky (RU)

Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: S1, S2 nebo S3.

Další informace najdete v části popisující [škálování](#scaling).

#### <a name="availability"></a>Dostupnost

|Název typu RU|Status|Datová centra
|---|---|---|
|S1|GA|Vše|
|S2|GA|Všechna kromě oblastí Brazílie – jih a Indie – západ|
|S3|GA|Všechna kromě oblasti Indie – západ|

## <a name="next-steps"></a>Další kroky

Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

