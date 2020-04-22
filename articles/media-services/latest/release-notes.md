---
title: Poznámky k verzi Azure Media Services v3 | Dokumenty společnosti Microsoft
description: Chcete-li zůstat aktuální s nejnovější vývoj, tento článek poskytuje nejnovější aktualizace na Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: b4849b4fbfdbaece46f5669f4c242e864b1ca533
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769750"
---
# <a name="azure-media-services-v3-release-notes"></a>Poznámky k verzi Azure Media Services v3

>Získejte upozornění, kdy se má tato stránka znovu aktualizovat zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` do čtečky informačních kanálů RSS.

Chcete-li mít aktuální informace o nejnovějším vývoji, tento článek obsahuje informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="known-issues"></a>Známé problémy

> [!NOTE]
> [Na portálu Azure](https://portal.azure.com/) můžete spravovat živé [události](live-events-outputs-concept.md)v3 , zobrazit [datové zdroje](assets-concept.md)v3 , získat informace o přístupu k virtuálním i matům. Pro všechny ostatní úlohy správy (například Transformace a úlohy) použijte [rozhraní REST API](https://aka.ms/ams-v3-rest-ref), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

Další informace naleznete v [tématu Migrace pokyny pro přechod z Media Services v2 v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2020"></a>Duben 2020

### <a name="improvements-in-documentation"></a>Vylepšení dokumentace

Dokumenty azure media playeru byly migrovány do [dokumentace k Azure](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Leden 2020

### <a name="improvements-in-media-processors"></a>Vylepšení mediálních procesorů

- Vylepšená podpora prokládaných zdrojů v analýze videa – takový obsah je nyní před odesláním do inference engines správně de-prokládán.
- Při generování miniatur v režimu "Nejlepší" kodér nyní hledá více než 30 sekund a vybere snímek, který není monochromatický.

### <a name="azure-government-cloud-updates"></a>Aktualizace cloudu Azure Government

Media Services GA'ed v následujících oblastech Azure governmentu: *USGov Arizona* a *USGov Texas*.

## <a name="december-2019"></a>Prosinec 2019

Přidána podpora CDN pro hlavičky *Origin-Assist Prefetch* pro živé i video na vyžádání; zákazníkům, kteří mají přímou smlouvu se společností Akamai CDN. Funkce CDN-Prefetch aplikace Origin-Assist zahrnuje následující výměny hlaviček PROTOKOLU HTTP mezi akamai CDN a původem služby Azure Media Services:

|Hlavička protokolu HTTP|Hodnoty|Odesílatel|Příjemce|Účel|
| ---- | ---- | ---- | ---- | ----- |
|CdN-Origin-Assist-Prefetch-Enabled CDN-Origin-Assist-Prefetch-Enabled CDN-Origin-Assist-Prefetch-Enabled CDN | 1 (výchozí) nebo 0 |CDN|Zdroj|Označení cdn je povoleno předběžné načtení.|
|CDN-Origin-Assist-Prefetch-Cesta| Příklad: <br/>Fragmenty(video=14000000000,format=mpd-time-cmaf)|Zdroj|CDN|Poskytnutí cesty předběžného načtení do sítě CDN|
|CDN-Origin-Assist-Prefetch-Request CDN-Origin-Assist-Prefetch-Request CDN-Origin-Assist-Prefetch-Request CDN|1 (žádost o předběžné načtení) nebo 0 (pravidelný požadavek)|CDN|Zdroj|Chcete-li označit požadavek z CDN je předběžné načtení|

Chcete-li zobrazit část výměny záhlaví v akci, můžete zkusit následující kroky:

1. Pomocí pošťáka nebo zvlnění můžete vydat žádost o původ mediálních služeb pro segment zvuku nebo videa nebo fragment. Ujistěte se, že v požadavku přidáte hlavičku CDN-Origin-Assist-Prefetch-Enabled: 1.
2. V odpovědi byste měli vidět záhlaví CDN-Origin-Assist-Prefetch-Path s relativní cestou jako jeho hodnotou.

## <a name="november-2019"></a>Listopad 2019

### <a name="live-transcription-preview"></a>Náhled živého přepisu

Živý přepis je nyní ve verzi Public Preview a je k dispozici pro použití v oblasti Západní USA 2.

Živý přepis je navržen tak, aby fungoval ve spojení s živými událostmi jako doplňková funkce.  Je podporována pro předávací i standardní nebo premium kódování živých událostí.  Pokud je tato funkce povolena, služba používá funkci [převodu řeči na text](../../cognitive-services/speech-service/speech-to-text.md) služby Cognitive Services k přepisu mluvených slov v příchozím zvuku do textu. Tento text je pak k dispozici pro doručení spolu s video a audio v MPEG-DASH a HLS protokoly. Fakturace je založena na novém měřiči doplňků, který je dodatečnými náklady na živou událost, když je ve stavu "Spuštěno".  Podrobnosti o živém přepisu a fakturaci najdete [v tématu Živý přepis](live-transcription.md)

> [!NOTE]
> V současné době je živý přepis k dispozici pouze jako funkce náhledu v oblasti Západní USA 2. Podporuje přepis mluvených slov v angličtině (en-us) pouze v tomto okamžiku.

### <a name="content-protection"></a>Ochrana obsahu

Funkce *Prevence přehrání tokenů,* která byla vydána v omezených oblastech v září, je nyní k dispozici ve všech oblastech.
Zákazníci služby Media Services mohou nyní nastavit omezení počtu, kolikrát lze stejný token použít k vyžádání klíče nebo licence. Další informace naleznete v tématu [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Noví doporučovaní partneři živého kodéru

Přidána podpora pro následující nové doporučené kodéry partnerů pro živé vysílání RTMP:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [Akční kamery GoPro Hero7/8 a Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Vylepšení kódování souborů

- Nyní je k dispozici nové přednastavení kódování s vědomím obsahu. Vytváří sadu GOP-zarovnané MP4 s použitím obsahu-zhotovení kódování. Vzhledem k tomu, jakýkoli vstupní obsah, služba provede počáteční zjednodušenou analýzu vstupního obsahu. Tyto výsledky používá k určení optimálního počtu vrstev, vhodné přenosové rychlosti a nastavení rozlišení pro doručování adaptivním streamováním. Toto přednastavení je obzvláště efektivní pro videa s nízkou složitostí a střední složitostí, kde jsou výstupní soubory s nižší přenosovou rychlostí, ale v kvalitě, která divákům stále přináší dobré prostředí. Výstup bude obsahovat MP4 soubory s video a audio prokládání. Další informace naleznete v [otevřených specifikacích rozhraní API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Zlepšený výkon a multi-threading pro re-sizer ve standardním kodéru. Za určitých podmínek by měl zákazník vidět zvýšení výkonu mezi 5-40% VOD kódování. Obsah s nízkou složitostí kódovaný do více přenosových rychlostí se zobrazí nejvyšší zvýšení výkonu. 
- Standardní kódování nyní udržuje pravidelnou kadenci GOP pro obsah s proměnnou snímkovou frekvencí (VFR) během kódování VOD při použití nastavení GOP založené na čase.  To znamená, že zákazník, který odesílá smíšený obsah kmitočet snímků, který se pohybuje mezi 15-30 fps, by měl nyní vidět pravidelné vzdálenosti GOP vypočítané na výstupu do adaptivního datového toku streamování souborů MP4. Tím se zlepší schopnost plynule přepínat mezi skladbami při doručování přes HLS nebo DASH. 
-  Vylepšená AV synchronizace pro zdrojový obsah s proměnnou snímkovou frekvencí (VFR)

### <a name="video-indexer-video-analytics"></a>Video Indexer, Analýza videa

- Klíčové snímky extrahované pomocí přednastavení VideoAnalyzer jsou nyní v původním rozlišení videa namísto velikosti. Extrakce klíčových snímků ve vysokém rozlišení poskytuje originální obrázky v kvalitě a umožňuje využívat modely umělé inteligence založené na obrazech poskytované službami Microsoft Computer Vision a Custom Vision, abyste získali ještě více poznatků z vašeho videa.

## <a name="september-2019"></a>Září 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Živé lineární kódování živých událostí

Media Services v3 oznamuje náhled 24 hodin x 365 dní živého lineárního kódování živých událostí.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Vyřazení mediálních procesorů

Oznamujeme vyřazení Azure *Media Indexer* a *Azure Media Indexer 2 Preview*. Data vyřazení naleznete v tématu [starších součástí.](../previous/legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) nahradí tyto starší mediální procesory.

Další informace najdete [v tématu Migrace z Azure Media Indexer a Azure Media Indexer 2 do Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Srpen 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Jižní Afrika regionální pár je otevřen pro mediální služby 

Mediální služby jsou nyní k dispozici v oblastech Jižní Afrika – severní a jižní Afrika – západ.

Další informace naleznete [v tématu Cloudy a oblasti, ve kterých media services v3 existuje](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Vyřazení mediálních procesorů

Oznamujeme vyřazení mediálních procesorů *Windows Azure Media Encoder* (WAME) a *Azure Media Encoder* (AME), které jsou vyřazeny. Data vyřazení naleznete v tomto tématu [starších součástí.](../previous/legacy-components.md)

Podrobnosti naleznete [v tématech Migrace wame do standardu kodéru médií](https://go.microsoft.com/fwlink/?LinkId=2101334) a migrace [ame do standardu kodéru médií](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Červenec 2019

### <a name="content-protection"></a>Ochrana obsahu

Při streamování obsahu chráněného omezením tokenu potřebují koncoví uživatelé získat token, který je odeslán jako součást požadavku na doručení klíče. Funkce *Prevence přehrání tokenu* umožňuje zákazníkům mediálních služeb nastavit limit, kolikrát lze stejný token použít k vyžádání klíče nebo licence. Další informace naleznete v tématu [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

Od července byla funkce náhledu dostupná pouze v usa – střed a v USA – střed.

## <a name="june-2019"></a>Červen 2019

### <a name="video-subclipping"></a>Dílčí dílčí vykložování videa

Nyní můžete oříznout nebo podklip video při kódování pomocí [job](https://docs.microsoft.com/rest/api/media/jobs). 

Tato funkce funguje s libovolnou [transformací,](https://docs.microsoft.com/rest/api/media/transforms) která je vytvořena pomocí [přednastavení BuiltInStandardEncoder Preset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) nebo [Přednastavení StandardEncoder.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Podívejte se na příklady:

* [Podklip k videu pomocí rozhraní .NET](subclip-video-dotnet-howto.md)
* [Podklip videa pomocí REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Květen 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Podpora Azure Monitor pro diagnostické protokoly a metriky mediálních služeb

Teď můžete použít Azure Monitor k zobrazení telemetrických dat vyzařovaných Media Services.

* Diagnostické protokoly Azure Monitor slouží ke sledování požadavků odeslaných koncovým bodem doručování klíčů mediálních služeb. 
* Sledujte metriky vyzařované [koncovými body datových proudů](streaming-endpoint-concept.md)datových proudů mediálních služeb .   

Podrobnosti naleznete [v tématu Metriky monitormedia services a diagnostické protokoly](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Podpora více zvukových stop v dynamickém balení 

Při streamování datových proudů, které mají více zvukových stop s více kodeky a jazyky, [dynamické balení](dynamic-packaging-overview.md) nyní podporuje více zvukových stop pro výstup HLS (verze 4 nebo vyšší).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionální pár je otevřen pro mediální služby 

Mediální služby jsou nyní k dispozici v oblastech Korea Central a Korea South. 

Další informace naleznete [v tématu Cloudy a oblasti, ve kterých media services v3 existuje](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Zlepšení výkonu

Přidány aktualizace, které zahrnují vylepšení výkonu mediálních služeb.

* Maximální velikost souboru podporovaná pro zpracování byla aktualizována. Viz, [Kvóty a limity](limits-quotas-constraints.md).
* [Vylepšení rychlosti kódování](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Duben 2019

### <a name="new-presets"></a>Nová přednastavení

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) byl přidán do přednastavení vestavěného analyzátoru.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) byl přidán do přednastavení přednastavení přednastavení přednastavení přednastavení předkodéru. Další informace naleznete v [tématu Kódování podle obsahu](content-aware-encoding.md). 

## <a name="march-2019"></a>Březen 2019

Dynamické balení nyní podporuje Dolby Atmos. Další informace naleznete [v tématu Zvukové kodeky podporované dynamickým balením](dynamic-packaging-overview.md#audio-codecs).

Nyní můžete zadat seznam datových zdrojů nebo filtrů účtů, který by se vztahoval na lokátor streamování. Další informace naleznete v tématu [Přidružení filtrů k lokátoru streamování](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Únor 2019

Mediální služby v3 se teď podporují v národních cloudech Azure. Ne všechny funkce jsou zatím k dispozici ve všech cloudech. Podrobnosti najdete v tématu [Cloudy a oblasti, ve kterých existuje Azure Media Services v3](azure-clouds-regions.md).

[Událost Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) byla přidána do schémat Azure Event Grid pro mediální služby.

## <a name="january-2019"></a>Leden 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Soubory Standard pro média a MPI 

Při kódování pomocí standardu Media Encoder Standard k vytvoření souborů MP4 je generován nový soubor MPI a přidán do výstupního datového zdroje. Tento soubor MPI je určen ke zlepšení výkonu pro [dynamické balení](dynamic-packaging-overview.md) a streamování scénáře.

Neměli byste upravovat nebo odebírat soubor MPI nebo převzít žádnou závislost ve vaší službě na existenci (nebo ne) takového souboru.

## <a name="december-2018"></a>Prosinec 2018

Aktualizace z ga verze Rozhraní API V3 zahrnují:
       
* Vlastnosti **PresentationTimeRange** již nejsou pro **filtry majetku** a **filtry účtů "vyžadovány"**. 
* Možnosti dotazu $top a $skip pro **úlohy** a transformace byly **odebrány** a $orderby byl přidán. V rámci přidání nové funkce řazení bylo zjištěno, že možnosti $top a $skip byly omylem vystaveny dříve, i když nejsou implementovány.
* Rozšiřitelnost výčtu byla znovu povolena. Tato funkce byla povolena ve verzích preview sady SDK a byla omylem zakázána ve verzi GA.
* Dvě předdefinované zásady streamování byly přejmenovány. **SecureStreaming** je nyní **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** je nyní **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Listopad 2018

Modul CLI 2.0 je teď k dispozici pro [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nové příkazy

- [az ams účet](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams účet-filtr](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams aktivum](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filtr](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams obsah-klíč-politika](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams práce](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-koncový bod](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-lokátor](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams účet mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - umožňuje spravovat media reserved units. Další informace naleznete v [tématu Měřítko rezervovaných jednotek médií](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nové funkce a nejnovější změny

#### <a name="asset-commands"></a>Příkazy majetku

- ```--storage-account```a ```--container``` přidány argumenty.
- Byly přidány výchozí hodnoty pro čas vypršení platnosti (Nyní ```az ams asset get-sas-url``` +23h) a oprávnění (Čtení).

#### <a name="job-commands"></a>Příkazy úlohy

- ```--correlation-data```a ```--label``` přidány argumenty
- ```--output-asset-names```přejmenovánna ```--output-assets```na . Nyní přijímá seznam prostředků oddělených mezerami ve formátu assetName=label. Datový zdroj bez popisku lze odeslat takto: "assetName=".

#### <a name="streaming-locator-commands"></a>Příkazy Lokátoru streamování

- ```az ams streaming locator```základní příkaz nahrazen ```az ams streaming-locator```.
- ```--streaming-locator-id```a ```--alternative-media-id support``` přidány argumenty.
- ```--content-keys argument```argument aktualizován.
- ```--content-policy-name```přejmenovánna ```--content-key-policy-name```na .

#### <a name="streaming-policy-commands"></a>Příkazy zásad streamování

- ```az ams streaming policy```základní příkaz nahrazen ```az ams streaming-policy```.
- Šifrování parametry ```az ams streaming-policy create``` podporují v přidané.

#### <a name="transform-commands"></a>Transformovat příkazy

- ```--preset-names```argument nahrazen ```--preset```. Nyní můžete nastavit pouze 1 výstup / přednastavení najednou (chcete-li přidat další, musíte spustit ```az ams transform output add```). Také můžete nastavit vlastní StandardEncoderPreset předáním cestu k vlastní JSON.
- ```az ams transform output remove```lze provést předáním výstupního indexu, který chcete odebrat.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```přidány argumenty ```az ams transform create``` ```az ams transform output add``` a příkazy.

## <a name="october-2018---ga"></a>Říjen 2018 - GA

Tato část popisuje aktualizace Azure Media Services (AMS) října.

### <a name="rest-v3-ga-release"></a>UVOLNĚNÍ REST v3 GA

[Verze REST v3 GA](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) obsahuje další api pro živé, filtry manifestu na úrovni účtu/majetku a podporu DRM.

#### <a name="azure-resource-management"></a>Správa prostředků Azure 

Podpora pro Azure Resource Management umožňuje jednotnou správu a provoz rozhraní API (nyní vše na jednom místě).

Počínaje touto verzí můžete k vytváření živých událostí použít šablony Správce prostředků.

#### <a name="improvement-of-asset-operations"></a>Zlepšení operací s aktivy 

Byla zavedena následující vylepšení:

- Ingestování z adres URL HTTP nebo adres URL úložiště objektů Blob Azure.
- Zadejte vlastní názvy kontejnerů pro datové zdroje. 
- Snadnější výstupní podpora pro vytváření vlastních pracovních postupů pomocí Azure Functions.

#### <a name="new-transform-object"></a>Nový objekt transformace

Nový **objekt Transformace** zjednodušuje model kódování. Nový objekt usnadňuje vytváření a sdílení kódování šablon a přednastavení Správce prostředků. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Ověřování azure služby Active Directory a RBAC

Ověřování Azure AD a řízení přístupu na základě rolí (RBAC) umožňují zabezpečené transformace, LiveEvents, zásady klíče obsahu nebo prostředky podle role nebo uživatelů ve službě Azure AD.

#### <a name="client-sdks"></a>Klientské sady SDK  

Jazyky podporované v Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Živé aktualizace kódování

Jsou zavedeny následující živé aktualizace kódování:

- Nový režim s nízkou latencí pro živé vysílání (10 sekund od konce do konce).
- Vylepšená podpora RTMP (zvýšená stabilita a větší podpora zdrojového kodéru).
- Bezpečné ingestování RTMPS.

    Když vytvoříte živou událost, získáte 4 adresy URL ingestování. 4 ingestující adresy URL jsou téměř identické, mají stejný token streamování (AppId), pouze číslo portu část se liší. Dvě adresy URL jsou primární a zálohy pro RTMPS. 
- Podpora překódování 24 hodin denně. 
- Vylepšená podpora ad signalizace v RTMP přes SCTE35.

#### <a name="improved-event-grid-support"></a>Vylepšená podpora event gridu

Můžete vidět následující vylepšení podpory Event Grid:

- Integrace Azure Event Grid pro snadnější vývoj s logic ovými aplikacemi a funkcemi Azure. 
- Přihlaste se k odběru událostí v kódování, živých kanálech a dalších.

### <a name="cmaf-support"></a>Podpora pro CMAF

Cmaf a 'cbcs' podpora šifrování pro Apple HLS (iOS 11 +) a MPEG-DASH přehrávače, které podporují CMAF.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA verze byla oznámena v srpnu. Nové informace o aktuálně podporovaných funkcích naleznete v tématu [Co je video indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plány změn

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Modul Azure CLI 2.0, který zahrnuje operace se všemi funkcemi (včetně živých, zásad obsahu, filtrů účtů/datových zdrojů, zásad streamování), se blíží. 

### <a name="known-issues"></a>Známé problémy

Následující problém má vliv pouze na zákazníky, kteří používali rozhraní API pro náhled pro datové zdroje nebo filtry accountfilterů.

Pokud jste vytvořili datové zdroje nebo filtry účtů mezi 09/28 a 10/12 s cli nebo api služby Media Services v3, je třeba odebrat všechny asset a AccountFilters a znovu je vytvořit z důvodu konfliktu verze. 

## <a name="may-2018---preview"></a>Květen 2018 - Náhled

### <a name="net-sdk"></a>.NET SDK

V sdk .NET SDK jsou k dispozici následující funkce:

* **Transformace** a **úlohy** pro kódování nebo analýzu mediálního obsahu. Příklady najdete v [tématu Stream ovat soubory](stream-files-tutorial-with-api.md) a [analyzovat](analyze-videos-tutorial-with-api.md).
* **Lokátory streamování** pro publikování a streamování obsahu do koncových uživatelských zařízení
* **Zásady streamování** a **zásady klíče obsahu** pro konfiguraci doručování klíčů a ochrany obsahu (DRM) při doručování obsahu.
* **Živé události** a **živé výstupy** pro konfiguraci ingestování a archivace živého streamování obsahu.
* **Prostředky** pro ukládání a publikování mediálního obsahu ve službě Azure Storage. 
* **Koncové body streamování** pro konfiguraci a škálování dynamického balení, šifrování a streamování pro živý i na vyžádání mediální obsah.

### <a name="known-issues"></a>Známé problémy

* Při odesílání úlohy můžete určit ingestování zdrojového videa pomocí adres URL HTTPS, adres URL SAS nebo cest k souborům umístěným v úložišti objektů Blob Azure. AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

- [Přehled](media-services-overview.md)
- [Aktualizace dokumentace služby Media Services v3](docs-release-notes.md)
- [Poznámky k verzi Media Services v2](../previous/media-services-release-notes.md)
