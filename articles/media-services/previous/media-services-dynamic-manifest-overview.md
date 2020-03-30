---
title: Filtry a dynamické manifesty | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak vytvořit filtry, aby je váš klient mohl použít k vysílání datového proudu určitých částí datového proudu. Media Services vytváří dynamické manifesty pro archivaci tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015856"
---
# <a name="filters-and-dynamic-manifests"></a>Filtry a dynamické manifesty

> [!div class="op_single_selector" title1="Vyberte verzi služby Media Services, kterou používáte:"]
> * [Verze 2](media-services-dynamic-manifest-overview.md)
> * [Verze 3](../latest/filters-dynamic-manifest-overview.md)

Počínaje verzí 2.17 umožňuje služba Media Services definovat filtry pro vaše datové zdroje. Tyto filtry jsou pravidla na straně serveru, která umožní zákazníkům zvolit si věci, jako je: přehrát pouze část videa (namísto přehrávání celého videa) nebo zadat pouze podmnožinu zvukových a video interpretací, které zařízení zákazníka zvládne ( namísto všech interpretací, které jsou přidruženy k prostředku). Tohoto filtrování vašich datových zdrojů je dosaženo prostřednictvím **dynamického manifestu,** které jsou vytvořeny na základě požadavku zákazníka na streamování videa na základě určených filtrů.

Toto téma popisuje běžné scénáře, ve kterých by použití filtrů bylo výhodné pro zákazníky a odkazy na témata, která ukazují, jak vytvářet filtry programově.

## <a name="overview"></a>Přehled
Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) je vaším cílem dodávat vysoce kvalitní video do různých zařízení za různých síťových podmínek. K dosažení tohoto cíle postupujte takto:

* zakódovat datový proud do vícebitového datového[toku (adaptivní datový tok)](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)video stream (to se postará o kvalitu a síťové podmínky) a 
* pomocí [dynamického balení](media-services-dynamic-packaging-overview.md) Mediálních služeb můžete dynamicky znovu zabalit datový proud do různých protokolů (to se postará o streamování na různých zařízeních). Služba Media Services podporuje poskytování následujících technologií adaptivního datového toku: HTTP Live Streaming (HLS), Smooth Streaming a MPEG DASH. 

### <a name="manifest-files"></a>Soubory manifestu
Když kódujete datový zdroj pro adaptivní streamování datového toku, vytvoří se soubor **manifestu** (playlist) (soubor je založen na textu nebo na XML). Soubor **manifestu** obsahuje streamovaná metadata, jako jsou: typ stopy (audio, video nebo text), název stopy, počáteční a koncový čas, datový tok (vlastnosti), jazyky stopy, okno prezentace (posuvné okno s pevnou dobou trvání), video kodek (FourCC). Také instruuje přehrávač, aby načetl další fragment tím, že poskytne informace o dalších dostupných fragmentech přehrávatelného videa a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" video obsahu.

Zde je příklad souboru manifestu: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dynamické manifesty
Existují [scénáře,](media-services-dynamic-manifest-overview.md#scenarios) kdy váš klient potřebuje větší flexibilitu, než je popsáno ve výchozím souboru manifestu datového zdroje. Například:

* Specifické pro zařízení: Doručujte pouze zadané interpretace a/nebo zadané jazykové skladby, které jsou podporovány zařízením, které se používá k přehrávání obsahu ("filtrování interpretací"). 
* Zmenšete manifest tak, aby se zobrazil dílčí klip živé události ("filtrování dílčích klipů").
* Ořízněte začátek videa ("oříznutí videa").
* Upravte prezentační okno (DVR) tak, aby poskytovalo omezenou délku okna DVR v přehrávači ("nastavení okna prezentace").

K dosažení této flexibility nabízí media services **dynamické manifesty** založené na předdefinovaných [filtrech](media-services-dynamic-manifest-overview.md#filters).  Po definování filtrů je mohou klienti použít k vysílání datových proudů určité interpretace nebo dílčích klipů videa. V adrese URL streamování by zadali filtry. Filtry lze použít pro adaptivní protokoly datových proudů podporované [dynamickým balením](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH a Smooth Streaming. Například:

Adresa URL MPEG DASH s filtrem

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Hladká adresa URL streamování s filtrem

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Další informace o tom, jak doručovat obsah a vytvářet adresy URL datových proudů, najdete v [tématu Doručování přehledu obsahu](media-services-deliver-content-overview.md).

> [!NOTE]
> Všimněte si, že dynamické manifesty nemění datový zdroj a výchozí manifest pro tento datový zdroj. Váš klient se může rozhodnout požádat o datový proud s filtry nebo bez něj. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filtry
Existují dva typy filtrů datových zdrojů: 

* Globální filtry (lze použít na libovolný datový zdroj v účtu Azure Media Services, mají životnost účtu) a 
* Místní filtry (lze použít pouze na datový zdroj, ke kterému byl filtr přidružen při vytvoření, mají životnost majetku). 

Globální a místní typy filtrů mají přesně stejné vlastnosti. Hlavní rozdíl mezi těmito dvěma je, pro které scénáře, jaký typ filer je vhodnější. Globální filtry jsou obecně vhodné pro profily zařízení (filtrování interpretací), kde by místní filtry mohly být použity k oříznutí konkrétního datového zdroje.

## <a name="common-scenarios"></a><a id="scenarios"></a>Obvyklé scénáře
Jak již bylo zmíněno dříve, při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) je vaším cílem dodávat vysoce kvalitní video do různých zařízení za různých síťových podmínek. Kromě toho můžete mít další požadavky, které zahrnují filtrování vašich datových zdrojů a používání **dynamického manifestu**s. V následujících částech je uveden stručný přehled různých scénářů filtrování.

* Zadejte pouze podmnožinu zvukových a obrazových interpretací, které mohou určitá zařízení zpracovat (namísto všech interpretací, které jsou přidruženy k datovému zdroji). 
* Přehrává pouze část videa (namísto přehrávání celého videa).
* Upravit okno prezentace DVR.

## <a name="rendition-filtering"></a>Filtrování interpretací
Můžete se rozhodnout kódovat datový zdroj do více profilů kódování (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) a více datových toky v kvalitě. Ne všechna klientská zařízení však budou podporovat všechny profily a datové sazby vašeho datového zdroje. Například starší zařízení android podporují pouze H.264 Baseline + AACL. Odesílání vyšších datových toků do zařízení, které nemůže získat výhody, ztrácí šířku pásma a výpočty zařízení. Takové zařízení musí dekódovat všechny dané informace, pouze proto, aby je zmenšeno pro zobrazení.

Pomocí dynamického manifestu můžete vytvářet profily zařízení, jako je mobilní zařízení, konzole, HD / SD atd., a zahrnout stopy a vlastnosti, které chcete být součástí každého profilu.

![Příklad filtrování interpretací][renditions2]

V následujícím příkladu byl k zakódování mezaninového datového zdroje použit kodér do sedmi interpretací videa ISO MP4 (od 180p do 1080p). Kódovaný datový zdroj lze dynamicky zabalit do některého z následujících protokolů streamování: HLS, Smooth a MPEG DASH.  V horní části diagramu je zobrazen manifest HLS pro datový zdroj bez filtrů (obsahuje všech sedm interpretací).  V levém dolním rohu je zobrazen manifest HLS, na který byl použit filtr s názvem "ott". Filtr "ott" určuje odstranění všech přenosových rychlostí nižších než 1 Mbps, což vedlo k tomu, že spodní dvě úrovně kvality byly v odpovědi odstraněny. V pravém dolním rohu je zobrazen manifest HLS, na který byl použit filtr s názvem "mobile". Filtr "mobilní" určuje odebrání interpretací, kde je rozlišení větší než 720p, což vedlo k odstranění dvou interpretací 1080p.

![Filtrování interpretací][renditions1]

## <a name="removing-language-tracks"></a>Odebrání jazykových stop
Vaše datové zdroje mohou zahrnovat více zvukových jazyků, jako je angličtina, španělština, francouzština atd. Sada Player SDK obvykle ve výchozím nastavení sady Player SDK výchozí výběr zvukové stopy a dostupné zvukové stopy na výběr uživatele. Je náročné rozvíjet takové sady Player SDK, vyžaduje různé implementace napříč rozhraními pro hráče specifické pro zařízení. Na některých platformách jsou také uživatelská api omezená a nezahrnují funkci výběru zvuku, kde uživatelé nemohou vybrat nebo změnit výchozí zvukovou stopu. Pomocí filtrů datových zdrojů můžete řídit chování vytvořením filtrů, které obsahují pouze požadované jazyky zvuku.

![Filtrování jazykových stop][language_filter]

## <a name="trimming-start-of-an-asset"></a>Oříznutí začátku datového zdroje
Ve většině živých přenosových událostí operátory spustit některé testy před skutečnou událost. Například mohou obsahovat břidlice, jako je tento před začátkem akce: "Program začne na okamžik". Pokud je program archivován, jsou data testu a břidlice také archivována a zahrnuta do prezentace. Tyto informace by však neměly být zobrazeny klientům. Pomocí dynamického manifestu můžete vytvořit filtr času zahájení a odebrat nežádoucí data z manifestu.

![Začátek oříznutí][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření dílčích klipů (zobrazení) z živého archivu
Mnoho živých událostí je dlouho spuštěno a živý archiv může zahrnovat více událostí. Po ukončení živé události mohou provozovatelé vysílání chtít rozdělit živý archiv na logické sekvence spuštění a zastavení programu. Dále publikujte tyto virtuální programy samostatně bez následného zpracování živého archivu a nevytváření samostatných datových zdrojů (což nevyužívá výhod stávajících fragmentů v mezipaměti v CDN). Příklady takových virtuálních programů jsou čtvrtiny fotbalového nebo basketbalového zápasu, směn v baseballu nebo jednotlivých událostí jakéhokoli sportovního programu.

Pomocí dynamického manifestu můžete vytvářet filtry pomocí počátečních a koncových časů a vytvářet virtuální zobrazení v horní části živého archivu. 

![Podklip, filtr][subclip_filter]

Filtrovaný majetek:

![Lyžování][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Nastavení okna prezentace (DVR)
V současné době Azure Media Services nabízí kruhový archiv, kde lze nakonfigurovat dobu trvání mezi 5 minut - 25 hodin. Filtrování manifestu lze použít k vytvoření postupného okna DVR nad horní částí archivu bez odstranění média. Existuje mnoho scénářů, kdy provozovatelé vysílání chtějí poskytnout omezené okno DVR pro pohyb s živým okrajem a současně zachovat větší okno archivace. Provozovatel vysílání může chtít použít data, která jsou z okna DVR ke zvýraznění klipů, nebo může chtít poskytnout různá okna DVR pro různá zařízení. Například většina mobilních zařízení nezpracovává velká okna DVR (můžete mít 2minutové okno DVR pro mobilní zařízení a jednu hodinu pro klienty pro stolní počítače).

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Nastavení LiveBackoff (živá poloha)
Filtrování manifestu lze použít k odstranění několika sekund z živého okraje živého programu. Filtrování umožňuje vysílatelům sledovat prezentaci v bodě publikace náhledu a vytvářet body vložení reklamy před tím, než diváci obdrží datový proud (zpětně o 30 sekund). Provozovatelé vysílání pak mohou tyto reklamy zasouvat do svých klientských rámců včas, aby je mohli obdržet a zpracovat informace před příležitostí k inzerci.

Kromě podpory reklamy livebackoff nastavení lze použít k úpravě pozice prohlížeče tak, aby při klienti drift a hit živé hrany mohou stále získat fragmenty ze serveru namísto získání chyby HTTP 404 nebo 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinace více pravidel v jednom filtru
V jednom filtru můžete kombinovat více pravidel filtrování. Jako příklad můžete definovat "pravidlo rozsahu" pro odstranění břidlic z živého archivu a také odfiltrovat dostupné přenosové rychlosti. Při použití více pravidel filtrování je konečným výsledkem průsečík všech pravidel.

![více pravidel][multiple-rules]

## <a name="create-filters-programmatically"></a>Programové vytváření filtrů
Následující článek popisuje entity služby Media Services, které souvisejí s filtry. Článek také ukazuje, jak programově vytvářet filtry.  

[Vytvořte filtry s rest API](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinace více filtrů (složení filtru)
Můžete také kombinovat více filtrů v jedné adrese URL. 

Následující scénář ukazuje, proč můžete chtít kombinovat filtry:

1. Je třeba filtrovat vlastnosti videa pro mobilní zařízení, jako je Android nebo iPAD (aby se omezily vlastnosti videa). Chcete-li odstranit nežádoucí vlastnosti, vytvořte globální filtr vhodný pro profily zařízení. Jak již bylo zmíněno dříve v tomto článku, globální filtry lze použít pro všechny vaše prostředky pod stejným účtem mediálních služeb bez dalšího přidružení. 
2. Chcete také zkrátit počáteční a koncový čas datového zdroje. Chcete-li toho dosáhnout, vytvoříte místní filtr a nastavíte čas zahájení a ukončení. 
3. Chcete kombinovat oba tyto filtry (bez kombinace je třeba přidat filtrování kvality do filtru oříznutí, což ztěžuje použití filtru).

Chcete-li kombinovat filtry, je třeba nastavit názvy filtrů na adresu URL manifestu/seznamu stop s vymezeným středníkem. Předpokládejme, že máte filtr s názvem *MyMobileDevice,* který filtruje vlastnosti a máte jiný s názvem *MyStartTime* nastavit konkrétní čas zahájení. Můžete je kombinovat takto:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Můžete kombinovat až tři filtry. 

Další informace naleznete v [tomto](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="know-issues-and-limitations"></a>Seznamte se s problémy a omezeními
* Dynamický manifest pracuje v gop hranice (klíčové snímky), tedy ořezávání má gop přesnost. 
* Pro místní a globální filtry můžete použít stejný název filtru. Místní filtry mají vyšší prioritu a přepíší globální filtry.
* Pokud aktualizujete filtr, může trvat až 2 minuty, než koncový bod streamování aktualizuje pravidla. Pokud byl obsah obsluhován pomocí některých filtrů (a ukládán do mezipaměti proxy a cdn), může aktualizace těchto filtrů způsobit selhání přehrávače. Po aktualizaci filtru doporučujeme vymazat mezipaměť. Pokud tato možnost není možná, zvažte použití jiného filtru.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Přehled doručování obsahu zákazníkům](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
