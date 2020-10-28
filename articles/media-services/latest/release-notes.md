---
title: Poznámky k verzi Azure Media Services V3 | Microsoft Docs
description: Abyste měli přehled o nejnovějším vývoji, najdete v tomto článku nejnovější aktualizace Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: d825a30cf43b3e7f9017638138df91d5145d280e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791592"
---
# <a name="azure-media-services-v3-release-notes"></a>Zpráva k vydání verze Azure Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` do čtečky kanálů RSS.

V tomto článku najdete informace o tom, jak se chcete zabývat aktuálním vývojem.

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="known-issues"></a>Známé problémy

> [!NOTE]
> [Azure Portal](https://portal.azure.com/) můžete použít ke správě [událostí V3 Live](live-events-outputs-concept.md), zobrazení [zdrojů](assets-concept.md) a úloh v3, získání informací o přístupu k rozhraním API a k šifrování obsahu. Pro všechny ostatní úlohy správy (například Správa transformací a úloh) použijte [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).
>
> Podrobnosti najdete v tématu [omezení Azure Portal pro Media Services V3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).

## <a name="october-2020"></a>Říjen 2020

### <a name="basic-audio-analysis"></a>Základní analýza zvuku
Přednastavení zvukové analýzy teď obsahuje cenovou úroveň základního režimu. Nový režim základní zvukové analyzátoru poskytuje možnost nízkého výkonu pro extrakci přepisu řeči a formátování titulků a titulků. Tento režim provádí přepis a generování VTT souboru titulků a titulků v textu. Výstup tohoto režimu zahrnuje soubor JSON Insights, včetně informací o klíčových slovech, přepisu a časování. V tomto režimu nejsou zahrnuté automatické rozpoznávání jazyka a diarizationy mluvčího. Podívejte se na seznam [podporovaných jazyků.](analyzing-video-audio-files-concept.md#built-in-presets)

Zákazníci, kteří používají indexer V1 a indexer v2, by měli migrovat na základní předvolby analýzy zvuku.

Další informace o režimu základní zvukové analyzátory najdete v tématu [Analýza videosouborů a zvukových souborů](analyzing-video-audio-files-concept.md).  Pokud se chcete dozvědět, jak použít režim základní zvukové analyzátory s REST API, přečtěte si téma [jak vytvořit základní transformaci zvuku](how-to-create-basic-audio-transform.md).

## <a name="live-events"></a>Živé události

Po zastavení živých událostí jsou nyní povoleny aktualizace většiny vlastností. Kromě toho můžou uživatelé zadat předponu pro statický název hostitele pro vstupní a náhledové adresy URL pro aktivní událost. VanityUrl je nyní volána `useStaticHostName` pro lepší reflektování záměru vlastnosti.

Živé události teď mají pohotovostní stav.  Podívejte [se na živé události a živé výstupy v Media Services](https://docs.microsoft.com/azure/media-services/latest/live-events-outputs-concept).

Živá událost podporuje příjem různých vstupních poměrů stran. Režim Stretch umožňuje zákazníkům určit chování roztažení výstupu.

Live Encoding teď přidává schopnost zasílat fragmenty intervalu pevného snímku s pevným klíčem mezi 0,5 až 20 sekund.

## <a name="august-2020"></a>Srpen 2020

### <a name="dynamic-encryption"></a>Dynamické šifrování
V dynamickém balíčku je teď k dispozici podpora starší verze formátu souboru s podporou technologie PlayReady Protected (PIFF 1,1). To poskytuje podporu pro starší sady inteligentních televizních pořadů od společnosti Samsung a LG, které implementovaly počáteční koncepty služby Common Encryption Standard (CENC) publikované Microsoftem.  Formát PIFF 1,1 se označuje také jako formát šifrování, který byl dříve podporován knihovnou klienta Silverlight. V současné době je jediným scénářem použití tohoto formátu šifrování zaměření na starší verzi inteligentního televizního vysílání, kde v některých oblastech zůstane netriviální počet inteligentních televizorů, které podporují jenom Smooth Streaming šifrování PIFF 1,1. 

Pokud chcete použít novou podporu šifrování PIFF 1,1, změňte hodnotu šifrování na PIFF v cestě URL lokátoru streamování. Další podrobnosti najdete v [přehledu Content Protection.](content-protection-overview.md)
Například: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> Podpora PIFF 1,1 je poskytována jako zpětně kompatibilní řešení pro inteligentní TV (Samsung, LG), které implementovalo úvodní verzi programu Common Encryption. Doporučuje se používat jenom formát PIFF, pokud je to potřeba pro podporu starších nebo LG inteligentních televizí dodaných mezi 2009-2015, které podporovaly verzi PIFF 1,1 šifrování PlayReady. 

## <a name="july-2020"></a>Červenec 2020

### <a name="live-transcriptions"></a>Živá Přepisy

Živý přepis teď podporuje 19 jazyků a 8 oblastí.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Ochrana obsahu pomocí Media Services a Azure AD

Publikovali jsme kurz nazvaný [kompletní ochrana obsahu pomocí Azure AD](./azure-ad-content-protection.md).

### <a name="high-availability"></a>Vysoká dostupnost

Zveřejnili jsme vysokou dostupnost s Media Services a [přehledem](./media-services-high-availability-encoding.md) a [ukázkou](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)videa na vyžádání (vod).

## <a name="june-2020"></a>Červen 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Live video Analytics v IoT Edge verze Preview

Verze Preview živé analýzy videí na IoT Edge se stala veřejnou. Další informace najdete v [poznámkách k verzi](../live-video-analytics-edge/release-notes.md).

Live video Analytics na IoT Edge je rozšířením řady mediálních služeb. Umožňuje analyzovat živé video s modely AI podle vašeho výběru na vlastních hraničních zařízeních a volitelně zachytit a zaznamenat toto video. Nyní můžete vytvářet aplikace s analýzou videa v reálném čase na hraničních zařízeních, aniž byste se museli starat o složitost sestavování a provozování živého video kanálu.

## <a name="may-2020"></a>Květen 2020

Azure Media Services je teď všeobecně dostupná v následujících oblastech: "Německo – sever", "Německo – středozápad", "Švýcarsko – sever" a "Švýcarsko – západ". Zákazníci mohou nasadit Media Services do těchto oblastí pomocí Azure Portal.

## <a name="april-2020"></a>Duben 2020

### <a name="improvements-in-documentation"></a>Vylepšení v dokumentaci

Do [dokumentace k Azure](../azure-media-player/azure-media-player-overview.md)byly migrovány Azure Media Player docs.

## <a name="january-2020"></a>Leden 2020

### <a name="improvements-in-media-processors"></a>Vylepšení v procesorech médií

- Vylepšená podpora prokládaných zdrojů v analýze videa – takový obsah je teď před odesláním do odvozených modulů nesprávně prokládaný.
- Když vygenerujete miniatury s využitím "nejlepšího" režimu, kodér nyní vyhledává rámec 30 sekund, aby vybral rámeček, který není monochromatický.

### <a name="azure-government-cloud-updates"></a>Azure Government aktualizace cloudu

Media Services GA'ed v následujících Azure Government oblastech: *USGov Arizona* a *USGov Texas* .

## <a name="december-2019"></a>Prosinec 2019

Přidala se podpora CDN pro *vyplněné hlavičky předběžného* načítání a pomoci pro živé streamování a streamování videa na vyžádání; k dispozici pro zákazníky, kteří mají přímý kontrakt s Akamai CDN. Funkce Origin-Assist CDN-Prefetch zahrnuje následující výměny hlaviček protokolu HTTP mezi Akamai CDN a Azure Media Services původu:

|Hlavička protokolu HTTP|Hodnoty|Odesílatel|Příjemce|Účel|
| ---- | ---- | ---- | ---- | ----- |
|CDN – počátek – pomoc – předběžné načtení – povoleno | 1 (výchozí) nebo 0 |CDN|Zdroj|K označení CDN je povolené předběžné načtení|
|CDN – počátek-asistence – předběžné načtení-cesta| Příklad: <br/>Fragmenty (video = 1400000000, Format = MPD-Time-CMAF)|Zdroj|CDN|Zadání cesty předběžného načtení do sítě CDN|
|CDN – počátek-asistence – předběžné načtení – požadavek|1 (požadavek na předběžné načtení) nebo 0 (pravidelný požadavek)|CDN|Zdroj|Pro indikaci, že žádost z CDN je předběžné načtení|

Pokud se chcete podívat na část výměny hlaviček v akci, můžete vyzkoušet následující postup:

1. Použijte metodu post nebo kudrlinkou k vystavení žádosti o Media Services původu zvukového nebo obrazového segmentu nebo fragmentu videa. Ujistěte se, že jste v žádosti přidali hlavičku CDN-počátek-asistenci-pomoc-předběžné načtení – povoleno: 1.
2. V odpovědi byste měli vidět hlavičku CDN-Origin-Assist-předběžného umístění s relativní cestou jako hodnotou.

## <a name="november-2019"></a>Listopad 2019

### <a name="live-transcription-preview"></a>Živý přepis Preview

Živý přepis je teď ve verzi Public Preview a dostupný pro použití v oblasti Západní USA 2.

Živý přepis je navržený tak, aby fungoval ve spojení s živými událostmi jako doplňkovou funkcí.  Podporuje se u živých událostí předávacího a standardního kódování nebo Premium.  Když je tato funkce povolená, služba použije funkci [řeči k textu](../../cognitive-services/speech-service/speech-to-text.md) Cognitive Services k přepisovat mluveného slova v příchozím zvukovém textu. Tento text je pak k dispozici pro doručování s videem a zvukem v protokolech MPEG-POMLČKy a HLS. Fakturace je založena na novém doplňkovém měřiči, který má za následek dodatečné náklady, když je ve stavu spuštěno.  Podrobnosti o živém přepisu a fakturaci najdete v tématu [živý přepis](live-transcription.md) .

> [!NOTE]
> V současné době je živý přepis dostupný jenom jako funkce Preview v oblasti Západní USA 2. V tuto chvíli podporuje přepis mluvených slov v angličtině (EN-US).

### <a name="content-protection"></a>Ochrana obsahu

Funkce *Prevence opětovného přehrání tokenu* , která byla vydaná v omezených oblastech zpátky v září, je teď dostupná ve všech oblastech.
Media Services zákazníci teď můžou nastavit limit počtu, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Další informace najdete v tématu [Prevence opětovného přehrání tokenu](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Noví doporučení partneři pro živé kodéry

Přidání podpory pro následující nové doporučené partnerské kodéry pro živé streamování RTMP:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [Kamery GoPro Hero7/8 a Max Action](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Vylepšení kódování souborů

- Nyní je k dispozici nová předvolba kódování s podporou obsahu. Vytváří sadu rychlostmi zarovnaných na skupinu GOP pomocí kódování s ohledem na obsah. Vzhledem k jakémukoli vstupnímu obsahu služba provádí počáteční odlehčenou analýzu vstupního obsahu. Tyto výsledky používá k určení optimálního počtu vrstev, vhodné přenosové rychlosti a nastavení rozlišení pro doručování pomocí adaptivního streamování. Tato předvolba je zvláště platná pro videa s nízkou složitostí a středními složitostmi, kde výstupní soubory jsou nižší, ale kvalita, která uživatelům nabízí dobré prostředí. Výstup bude obsahovat soubory MP4 se zakládaným videem a zvukem. Další informace najdete v tématu o [otevřených specifikacích rozhraní API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Vylepšený výkon a multithreading pro službu resizeer ve standardním kodéru. Za určitých podmínek by měl zákazník vidět zvýšení výkonu mezi 5-40% kódováním VOD. Obsah s nízkou složitostí kódovaný do více přenosových rychlostí uvidí nejvyšší zvýšení výkonu. 
- Kódování standard teď při použití nastavení skupinu GOP založeného na čase udržuje regulární skupinu GOP tempo pro obsah VFR (Variable snímkové frekvence) během kódování VOD.  To znamená, že zákazník, který posílá smíšený obsah snímků, který se v různých intervalech škáluje 15-30 fps, by teď měl zobrazovat běžné skupinu GOPé vzdálenosti vypočítané na výstupu pro streamování souborů MP4 s adaptivní přenosovou rychlostí. Tím se zvýší schopnost plynule přepínat mezi stopami při doručování přes HLS nebo POMLČKy. 
-  Vylepšená synchronizace AV pro VFR (variabilní snímková frekvence) zdrojového obsahu

### <a name="video-indexer-video-analytics"></a>Video Indexer, analýza videa

- Klíčové snímky extrahované pomocí přednastavené VideoAnalyzer jsou teď v původním rozlišení videa, místo aby se změnila velikost. Extrakce klíčových snímků s vysokým rozlišením poskytuje originální kvalitní image a umožňuje používat modely umělých analýz založené na obrázcích, které poskytuje Microsoft Počítačové zpracování obrazu a Custom Vision, aby bylo možné získat ještě více přehledů z vašeho videa.

## <a name="september-2019"></a>Září 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Živé lineární kódování pro živé události

Media Services V3 oznamuje verzi Preview 24 hodin x 365 dní živého lineárního kódování živých událostí.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Vyřazení procesorů médií

Oznamujeme vyřazení *Azure Media Indexer* a *Azure Media Indexer 2 ve verzi Preview* . Data o vyřazení najdete v článku  [starší verze součástí](../previous/legacy-components.md) . [Azure Media Services video indexer](../video-indexer/index.yml) nahrazuje tyto starší verze procesorů médií.

Další informace najdete v tématu [migrace z Azure Media Indexer a Azure Media Indexer 2 na Azure Media Services video indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Srpen 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Pro Media Services je otevřen regionální pár jar. 

Media Services je teď k dispozici v oblasti Jižní Afrika – sever a Jižní Afrika – západ.

Další informace najdete v tématu [cloudy a oblasti, ve kterých existuje Media Services V3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Vyřazení procesorů médií

Oznamujeme, že vyřadíme nepoužívané procesory *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (ázev), které jsou vyřazené. Informace o datech vyřazení najdete v článku tyto [starší součásti](../previous/legacy-components.md) .

Podrobnosti najdete v článku [migrace WAME do Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) a [migrace do Media Encoder Standard](../previous/migrate-azure-media-encoder.md).
 
## <a name="july-2019"></a>Červenec 2019

### <a name="content-protection"></a>Ochrana obsahu

Při streamování obsahu chráněného omezením tokenu musí koncoví uživatelé získat token, který se odešle jako součást žádosti o doručení klíče. Funkce *Prevence opětovného přehrání tokenu* umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Další informace najdete v tématu [Prevence opětovného přehrání tokenu](content-protection-overview.md#token-replay-prevention).

Od července byla funkce Preview dostupná jenom v USA – střed a USA – středozápad.

## <a name="june-2019"></a>Červen 2019

### <a name="video-subclipping"></a>Dílčí výstřižek videa

Video teď můžete při kódování pomocí [úlohy](/rest/api/media/jobs)oříznout nebo vystřihnout. 

Tato funkce funguje s libovolnou [transformací](/rest/api/media/transforms) , která je sestavená buď pomocí přednastavení [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) , nebo z [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) předvoleb. 

Viz příklady:

* [Vytvoření dílčího klipu videa s využitím .NET](subclip-video-dotnet-howto.md)
* [Vytvoření dílčího klipu videa s REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Květen 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor podporu pro diagnostické protokoly a metriky Media Services

Nyní můžete použít Azure Monitor k zobrazení dat telemetrie emitovaných Media Services.

* Pomocí diagnostických protokolů Azure Monitor můžete monitorovat požadavky odeslané koncovým bodem pro doručení Media Services Key. 
* Monitoruje metriky vydávané Media Services [koncovými body streamování](streaming-endpoint-concept.md).   

Podrobnosti najdete v tématu [monitorování metrik Media Services a diagnostických protokolů](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Podpora více zvukových stop v dynamickém balení 

Při streamování assetů, které mají více zvukových stop s více kodeky a jazyky, teď [Dynamická balení](dynamic-packaging-overview.md) podporuje více zvukových stop pro výstup HLS (verze 4 nebo vyšší).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Oblast Korea je otevřená pro Media Services 

Media Services je teď k dispozici v oblasti Korea – střed a Korea – jih. 

Další informace najdete v tématu [cloudy a oblasti, ve kterých existuje Media Services V3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Vylepšení výkonu

Byly přidány aktualizace, které zahrnují vylepšení výkonu Media Services.

* Maximální velikost souboru podporovaná pro zpracování byla aktualizována. Viz, [kvóty a omezení](limits-quotas-constraints.md).
* [Vylepšení rychlosti kódování](concept-media-reserved-units.md).

## <a name="april-2019"></a>Duben 2019

### <a name="new-presets"></a>Nové předvolby

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) se přidal do vestavěných přednastavení analyzátoru.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) se přidal do vestavěných přednastavení kodéru. Další informace najdete v tématu [kódování s ohledem na obsah](content-aware-encoding.md). 

## <a name="march-2019"></a>Březen 2019

Dynamické balení teď podporuje Dolby ATMOS. Další informace najdete v tématu [zvukové kodeky podporované dynamickým balením](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Teď můžete určit seznam filtrů Asset nebo Account, které se vztahují na Lokátor streamování. Další informace najdete v tématu [přidružení filtrů k lokátoru streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Únor 2019

V národních cloudech Azure se teď podporuje Media Services V3. Ne všechny funkce jsou zatím dostupné ve všech cloudech. Podrobnosti najdete v tématu [cloudy a oblasti, ve kterých existuje Azure Media Services V3](azure-clouds-regions.md).

Do schémat Azure Event Grid byla přidána událost [Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) pro Media Services.

## <a name="january-2019"></a>Leden 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Soubory Media Encoder Standard a MPI 

Při kódování s Media Encoder Standard k vytvoření souborů MP4 se vygeneruje nový soubor. MPI a přidá se do výstupního prostředku. Tento soubor MPI má za cíl zlepšit výkon pro [dynamické balení](dynamic-packaging-overview.md) a streamování.

Soubor MPI byste neměli upravovat ani odebírat nebo v rámci služby nemusíte mít žádnou závislost na existenci (nebo ne) takového souboru.

## <a name="december-2018"></a>Prosinec 2018

Mezi verze V3 rozhraní API pro aktualizace od verze GA patří:
       
* Pro **filtry prostředků** a **filtry účtu** se už nevyžadují vlastnosti **PresentationTimeRange** . 
* Možnosti $top a $skip dotazu pro **úlohy** a **transformace** byly odebrány a $OrderBy byly přidány. V rámci přidávání nové funkce řazení bylo zjištěno, že $top a $skip možnosti byly omylem vystaveny dříve, i když nejsou implementovány.
* Rozšiřitelnost výčtu byla znovu povolena. Tato funkce byla povolená ve verzi Preview sady SDK a v rámci verze GA se nechtěně vypnula.
* Dva předdefinované zásady streamování se přejmenovaly. **SecureStreaming** je teď **MultiDrmCencStreaming** . **SecureStreamingWithFairPlay** je nyní **Predefined_MultiDrmStreaming** .

## <a name="november-2018"></a>Listopad 2018

Modul CLI 2,0 je teď dostupný pro [Azure Media Services V3 GA](/cli/azure/ams?view=azure-cli-latest&preserve-view=true) – v 2.0.50.

### <a name="new-commands"></a>Nové příkazy

- [AZ AMS Account](/cli/azure/ams/account?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Account-Filter](/cli/azure/ams/account-filter?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Asset](/cli/azure/ams/asset?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Asset-Filter](/cli/azure/ams/asset-filter?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Content-Key-Policy](/cli/azure/ams/content-key-policy?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Job](/cli/azure/ams/job?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Live-Event](/cli/azure/ams/live-event?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Live-Output](/cli/azure/ams/live-output?view=azure-cli-latest&preserve-view=true)
- [AZ AMS streaming-Endpoint](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest&preserve-view=true)
- [AZ AMS streaming – Lokátor](/cli/azure/ams/streaming-locator?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest&preserve-view=true) -umožňuje správu rezervovaných jednotek médií. Další informace najdete v tématu [škálování rezervovaných jednotek médií](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nové funkce a zásadní změny

#### <a name="asset-commands"></a>Příkazy assetu

- ```--storage-account``` a ```--container``` přidané argumenty.
- Výchozí hodnoty pro čas vypršení platnosti (nyní + 23H) a oprávnění (čtení) v ```az ams asset get-sas-url``` příkazu byl přidán.

#### <a name="job-commands"></a>Příkazy úlohy

- ```--correlation-data``` a ```--label``` přidané argumenty
- ```--output-asset-names``` přejmenování na ```--output-assets``` . Nyní přijímá seznam assetů oddělených mezerou ve formátu "název prostředku". Prostředek bez popisku se dá odeslat takto: "Asset =".

#### <a name="streaming-locator-commands"></a>Příkazy lokátoru streamování

- ```az ams streaming locator``` základní příkaz nahrazen ```az ams streaming-locator``` .
- ```--streaming-locator-id``` a ```--alternative-media-id support``` přidané argumenty.
- ```--content-keys argument``` argument se aktualizoval.
- ```--content-policy-name``` přejmenování na ```--content-key-policy-name``` .

#### <a name="streaming-policy-commands"></a>Příkazy zásad streamování

- ```az ams streaming policy``` základní příkaz nahrazen ```az ams streaming-policy``` .
- Podporuje parametry šifrování v ```az ams streaming-policy create``` přidaných.

#### <a name="transform-commands"></a>Příkazy transformace

- ```--preset-names``` Argument byl nahrazen parametrem ```--preset``` . Nyní můžete nastavit pouze 1 výstup/přednastavení současně (Pokud chcete přidat více, je třeba spustit ```az ams transform output add``` ). Můžete také nastavit vlastní StandardEncoderPreset předáním cesty k vlastnímu formátu JSON.
- ```az ams transform output remove``` dá se provést předáním výstupního indexu, který se má odebrat.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumenty přidané v ```az ams transform create``` ```az ams transform output add``` příkazech a.

## <a name="october-2018---ga"></a>Říjen 2018 – GA

Tato část popisuje Azure Media Services (AMS) Říjen Updates.

### <a name="rest-v3-ga-release"></a>Verze REST v3 pro GA

[Verze REST v3 pro ga](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zahrnuje více rozhraní API pro filtry manifestu Live, Account/Asset Level a podpora DRM.

#### <a name="azure-resource-management"></a>Správa prostředků Azure 

Podpora správy prostředků Azure umožňuje sjednocené rozhraní API pro správu a provoz (teď všechno na jednom místě).

Od této verze můžete použít šablony Správce prostředků k vytvoření živých událostí.

#### <a name="improvement-of-asset-operations"></a>Zlepšení provozu prostředků 

Byla představena následující vylepšení:

- Ingestování z adres URL protokolu HTTP (s) nebo Blob Storage adres URL SAS Azure
- Zadejte vlastní názvy kontejnerů pro prostředky. 
- Snadnější podpora výstupu při vytváření vlastních pracovních postupů pomocí Azure Functions.

#### <a name="new-transform-object"></a>Nový objekt transformace

Nový objekt **transformace** zjednodušuje model kódování. Nový objekt usnadňuje vytváření a sdílení kódování Správce prostředků šablon a přednastavení. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Ověřování Azure Active Directory a Azure RBAC

Ověřování Azure AD a řízení přístupu na základě role Azure (Azure RBAC) umožňují zabezpečené transformace, LiveEvents, zásady klíčů obsahu nebo prostředky podle rolí nebo uživatelů v Azure AD.

#### <a name="client-sdks"></a>Klientské sady SDK  

Jazyky podporované v Media Services V3: .NET Core, Java, Node.js, Ruby, TypeScript, Python, přejít.

#### <a name="live-encoding-updates"></a>Živé aktualizace kódování

Zavádí se následující aktualizace pro živé kódování:

- Nový režim s nízkou latencí pro živý (10 sekund od začátku do konce).
- Vylepšená podpora RTMP (zvýšená stabilita a lepší podpora zdrojového kodéru).
- Zabezpečení pro ingestování RTMP

    Při vytváření živé události teď dostanete čtyři adresy URL pro příjem. 4 adresy URL pro přijímání jsou skoro stejné, mají stejný token streamování (AppId), ale liší se jenom část číslo portu. Dvě z těchto adres URL jsou primární a zálohují pro RTMP. 
- Podpora překódování po dobu 24 hodin. 
- Vylepšená podpora služby AD-Signaling v RTMP přes SCTE35.

#### <a name="improved-event-grid-support"></a>Vylepšená podpora Event Grid

Můžete si prohlédnout následující Event Grid vylepšení podpory:

- Azure Event Grid Integration pro snazší vývoj s Logic Apps a Azure Functions. 
- Přihlaste se k odběru událostí pro kódování, živé kanály a další.

### <a name="cmaf-support"></a>Podpora CMAF

CMAF a podpora šifrování "cbcs" pro Apple HLS (iOS 11 +) a přehrávače MPEG-SPOJOVNÍKů, které podporují CMAF.

### <a name="video-indexer"></a>Video Indexer

Vydání Video Indexer GA bylo oznámeno v srpnu. Nové informace o aktuálně podporovaných funkcích najdete v tématu [co je video indexer](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### <a name="plans-for-changes"></a>Plánuje změny

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Připravuje se modul Azure CLI 2,0, který obsahuje operace se všemi funkcemi (včetně živých, zásad pro klíče obsahu, filtrů účtů a assetů, zásad streamování). 

### <a name="known-issues"></a>Známé problémy

Následující problém mají vliv jenom na zákazníky, kteří použili rozhraní API pro verzi Preview pro Asset nebo AccountFilters.

Pokud jste vytvořili filtry prostředků nebo účtů mezi 09/28 a 10/12 pomocí Media Services V3 CLI nebo rozhraní API, je potřeba odebrat všechny Assety a AccountFilters a znovu je vytvořit z důvodu konfliktu verzí. 

## <a name="may-2018---preview"></a>Květen 2018 – Preview

### <a name="net-sdk"></a>.NET SDK

V sadě .NET SDK jsou k dispozici následující funkce:

* **Transformuje** a **úlohy** ke kódování nebo analýze mediálního obsahu. Příklady najdete v tématu [streamování souborů](stream-files-tutorial-with-api.md) a [Analýza](analyze-videos-tutorial-with-api.md).
* **Lokátory streamování** pro publikování a streamování obsahu do zařízení koncových uživatelů
* Zásady **streamování** a **zásady klíčů obsahu** ke konfiguraci doručování klíčů a ochrany obsahu (DRM) při doručování obsahu.
* **Živé události** a **živé výstupy** umožňují konfigurovat ingestování a archivaci obsahu živého streamování.
* **Prostředky** pro ukládání a publikování mediálního obsahu v Azure Storage. 
* **Koncové body streamování** , které slouží ke konfiguraci a škálování dynamického balení, šifrování a streamování pro živý i mediální obsah na vyžádání.

### <a name="known-issues"></a>Známé problémy

* Při odesílání úlohy můžete určit, že se má vaše zdrojové video ingestovat pomocí adres URL protokolu HTTPS, adres URL SAS nebo cest k souborům umístěným v úložišti objektů BLOB v Azure. V současné době Media Services V3 nepodporuje kódování bloků přenosu prostřednictvím adres URL protokolu HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="see-also"></a>Viz také

[Pokyny k migraci pro přesun z Media Services V2 na V3](migrate-from-v2-to-v3.md#known-issues).

## <a name="next-steps"></a>Další kroky

- [Přehled](media-services-overview.md)
- [Aktualizace dokumentace k Media Services V3](docs-release-notes.md)
- [Poznámky k verzi Media Services V2](../previous/media-services-release-notes.md)