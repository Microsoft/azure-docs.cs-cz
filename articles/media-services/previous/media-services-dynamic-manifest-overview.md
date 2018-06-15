---
title: Filtry a dynamické manifesty | Microsoft Docs
description: Toto téma popisuje, jak vytvářet filtry, takže vašeho klienta můžete použít datový proud určité části datového proudu. Služba Media Services vytvoří dynamické manifesty k archivaci selektivní streamování.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/22/2018
ms.author: cenkd;juliako
ms.openlocfilehash: 982af37a866f73292192b0c889e9eeb1e1291030
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788869"
---
# <a name="filters-and-dynamic-manifests"></a>Filtry a dynamické manifestů
Od verze 2.17, Media Services umožňuje definovat filtry pro vaše prostředky. Tyto filtry jsou pravidla na straně serveru, které vám umožní vašim zákazníkům, kde můžete provádět například následující akce: přehrávání pouze část videa (namísto přehrávání celou video), nebo zadejte pouze podmnožinu interpretace audia a videa, které může zařízení vašich zákazníků (místo toho zpracovat všechny interpretací, jsou přidružený asset). Tento filtrování vaše prostředky bude archivován prostřednictvím **dynamické Manifest**ů, které jsou vytvořené na žádost zákazníka Streamovat videa podle zadané filtry.

Tato témata popisuje běžné scénáře, ve kterém pomocí filtrů by bylo velmi užitečné k vašim zákazníkům a odkazy na témata, které ukazují, jak vytvářet filtry prostřednictvím kódu programu.

## <a name="overview"></a>Přehled
Při doručování obsahu zákazníkům (streaming živé události nebo vyžádání, video-on-demand) je vaším cílem poskytování vysoce kvalitního videa pro různá zařízení v různých síťových podmínkách. K dosažení tohoto cíle provést následující kroky:

* Kódovat datový proud do více přenosovými rychlostmi ([s adaptivní přenosovou rychlostí](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) datový proud videa (to se postará o kvalitu i síťové podmínky) a 
* používání Media Services [dynamické balení](media-services-dynamic-packaging-overview.md) a dynamicky znovu zabalte datový proud do jiné protokoly (to se postará o streamování na různá zařízení). Služba Media Services podporuje doručování následujících adaptivní přenosové rychlosti streamování technologie: HTTP Live Streaming (HLS), technologie Smooth Streaming a MPEG DASH. 

### <a name="manifest-files"></a>Soubory manifestu
Při kódování prostředek pro streamování s adaptivní přenosovou rychlostí **manifest** se vytvoří soubor (STOP) (soubor je založený na textu nebo na základě XML). **Manifest** soubor obsahuje metadata, jako streamování: sledování typu (zvuk, video nebo text), sledovat název, počáteční a koncový čas, přenosovou rychlostí (Vlastnosti), sledovat jazyky, prezentace okno (posuvné okno Pevná doba trvání), (kodek videa FourCC). Také dá pokyn player pro načtení další fragment tím, že poskytuje informace o na další možné přehrát video fragmenty dostupné a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky dat" video obsahu.

Tady je příklad souboru manifestu: 

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

### <a name="dynamic-manifests"></a>Dynamické manifestů
Existují [scénáře](media-services-dynamic-manifest-overview.md#scenarios) když váš klient potřebuje více flexibility než co je popsaný v souboru manifestu výchozí asset. Příklad:

* Zařízení, konkrétní: poskytovat sleduje jazyk, které podporuje zařízení, která je zadána nebo zadaný interpretace pouze používá k přehrávání obsahu ("interpretace filtrování"). 
* Snižte manifest zobrazíte dílčí klip živé události ("dílčí klip filtrování").
* Trim – začátek video ("ořezávání video").
* Upravte prezentace okno (DVR) Chcete-li poskytovat omezené délky okna formátu DVR v player ("okno s úpravou prezentace").

K dosažení tuto flexibilitu potřebují, Media Services nabízí **dynamické manifesty** založené na předem definované [filtry](media-services-dynamic-manifest-overview.md#filters).  Jakmile definujete filtry, vaši klienti využít k vysílání datového proudu konkrétní interpretace nebo dílčí klipů videa. Filtry by zadanému v adrese URL streamování. Použít filtry i adaptivní přenosové rychlosti streamování protokolů podporovaných [dynamické balení](media-services-dynamic-packaging-overview.md): HLS, MPEG DASH a technologie Smooth Streaming. Příklad:

Adresa URL MPEG DASH s filtrem

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Technologie Smooth Streaming adresu URL pomocí filtru

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Další informace o tom, jak poskytování obsahu a sestavení adresy URL pro streamování najdete v tématu [doručování obsahu přehled](media-services-deliver-content-overview.md).

> [!NOTE]
> Všimněte si, že dynamické manifesty neměňte asset a výchozí manifest pro tento prostředek. Váš klient můžete k vyžádání datového proudu s nebo bez filtry. 
> 
> 

### <a id="filters"></a>filtry
Existují dva typy filtrů asset: 

* Globální filtry (lze použít pro prostředek v účtu Azure Media Services, mají životnost účet) a 
* Místní filtry (můžete použít pouze pro prostředek, ke kterému se vztahovaly po vytvoření filtru, mají životnost asset). 

Globální a místní filtrování typů mají stejné vlastnosti. Hlavní rozdíl mezi nimi je, jaký typ souborového pro scénáře, které je vhodnější. Globální filtry jsou obecně vhodné pro profily zařízení (interpretace filtrování), kde místní filtry může trim konkrétní asset.

## <a id="scenarios"></a>Běžné scénáře
Jak již bylo zmíněno, než při doručování obsahu zákazníkům (streaming živé události nebo vyžádání, video-on-demand) vaším cílem je k poskytování vysoce kvalitního videa pro různá zařízení v různých síťových podmínkách. Kromě toho vaší může mít jiné požadavky, které zahrnují filtrování vaše prostředky a použití **dynamické Manifest**s. Následujících oddílech najdete stručný přehled různých scénářů filtrování.

* Zadejte jenom podmnožinu interpretace audia a videa, které může zpracovat určitých zařízení (ne všechny interpretace, které jsou přidruženy asset). 
* Přehrávání pouze část videa (namísto přehrávání celou video).
* Upravte formátu DVR prezentace okno.

## <a name="rendition-filtering"></a>Interpretace filtrování
Můžete se rozhodnout, kódovat asset víc kódování profilů (H.264 směrného plánu, H.264 vysoké, AACL, AACH, Dolby Digital Plus) a více přenosových rychlostí kvality. Ne všechna zařízení klienta bude však podporovat všechny assetu profily a přenosových rychlostí. Například starší zařízení se systémem Android podporuje pouze H.264 směrného plánu + AACL. Vyšší přenosových rychlostí odesílání do zařízení, které nelze získat výhody, zbytečně plýtvá výpočet šířky pásma a zařízení. Taková zařízení musí dekódovat všechny informace o daném, jenom na snižovat pro zobrazení.

Pomocí dynamických Manifest můžete vytvářet profily zařízení například mobile, konzoly, HD/SD atd. a zahrnují sleduje a vlastnosti, které mají být součástí každý profil.

![Příklad filtrování interpretace][renditions2]

V následujícím příkladu se použila kodér ke kódování mezzanine asset do sedmi interpretace video soubory MP4 s rychlostmi ISO (z 180p 1080p). K zakódovanému assetu se dá dynamicky zabalit do jakéhokoli z těchto protokolů streamování: HLS, Smooth a MPEG DASH.  V horní části diagramu, se zobrazí HLS manifest pro prostředek s žádné filtry (obsahuje všechny sedm interpretace).  V dolní části vlevo se zobrazí v manifestu HLS, do které byl použit filtr s názvem "ott". Určuje filtr "ott" Odebrat všechny přenosových rychlostí nižší než 1 MB/s, čímž vznikl dolní dvě úrovně kvality se odstraní v odpovědi.  V vpravo dole se zobrazí v manifestu HLS, do které byl použit filtr s názvem "mobilní". Určuje filtr "mobilní" Odebrat interpretace, kde je větší než 720p, čímž vznikl dvě řešení 1080p interpretace se odstraní a.

![Interpretace filtrování][renditions1]

## <a name="removing-language-tracks"></a>Odebírání sleduje jazyk
Vaše prostředky může obsahovat více zvuk jazyků, jako je angličtina, španělština, francouzština, atd. Obvykle správci Player SDK výchozí výběr zvukové stopy a k dispozici zvuk sleduje za výběr uživatele. Je obtížné vyvíjet takové sady SDK Player, vyžaduje různými implementacemi napříč konkrétní zařízení architektury přehrávačů. Na některých platformách také Player rozhraní API jsou omezené a nezahrnují funkce audia výběr kde nelze uživatelů vyberte nebo změňte sledování zvuk výchozí. S filtry asset můžete řídit chování vytvořením filtry, které pouze zahrnout požadované zvuk jazyky.

![Sleduje jazyka filtrování][language_filter]

## <a name="trimming-start-of-an-asset"></a>Oříznutí začátek prostředek
Ve většině živě streamovaných událostí operátoři spustit některé testy před skutečné události. Například může zahrnovat projektem takto před zahájením události: "Programu zbývá na okamžik". Pokud je program archivace, test a projektem data jsou také archivovat a součástí prezentaci. Tyto informace však by neměly být uváděny klientům. S dynamické manifestu můžete vytvořit filtr času zahájení a odebrat nežádoucí data z manifestu.

![Oříznutí start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření z archivu živé subclips (zobrazení)
Mnoho živé události jsou dlouho spuštěný a za provozu archivu může obsahovat více událostí. Po skončení živé události, chtít televizního rozdělit za provozu archivu do logické program spuštění a zastavení pořadí. V dalším kroku publikujte samostatně tyto virtuální programy bez post zpracování archivu za provozu a nevytváří samostatné prostředky (který není těžit z existující uložené v mezipaměti fragmenty v sítím CDN). Mezi tyto virtuální programy jsou čtvrtletí fotbalové nebo Basketbalový hra, innings v baseballové nebo jednotlivé události žádné program sportu.

S dynamické Manifest můžete vytvářet filtry, pomocí počáteční/koncový čas a vytvořit virtuální zobrazení v horní části vaší živé archivu. 

![Subclip filtru][subclip_filter]

Filtrované Asset:

![Lyžování][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Nastavení prezentace okno (DVR)
V současné době Azure Media Services nabízí cyklické archivu, kde můžete trvání nakonfigurované mezi 5 minut – 25 hodin. Manifestu filtrování slouží k vytvoření postupného formátu DVR okna v horní části archivu, bez odstranění média. Existuje mnoho scénářů, kde chcete poskytnout okno omezené formátu DVR přesunout s hranou za provozu a současně zachovat větší archivační okno televizního. Zdroje všesměrového vysílání může používat data, která je mimo okno formátu DVR, zvýrazněte klipů, nebo může být vhodné he\she poskytují různé formátu DVR windows pro různá zařízení. Většina mobilních zařízení například nemáte zpracovávat velké formátu DVR windows (může mít 2 minuty formátu DVR okna pro mobilní zařízení a jednu hodinu klientů plochy).

![Okno formátu DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Úprava LiveBackoff (živé pozice)
Manifestu filtrování lze použít k odebrání za provozu hrany živou program několik sekund. Filtrování umožňuje televizního podívejte na prezentaci v bodě publikace preview a vytvořit oznámení o inzerovaném programu body vložení předtím, než prohlížeče zobrazí datový proud (zálohovaný vypnuto 30 sekund). Televizního můžete nabízená těchto oznámení o inzerovaném programu jejich rozhraní klienta v čase se přijaté a zpracovat informace před možnost oznámení o inzerovaném programu.

Kromě podpory oznámení o inzerovaném programu LiveBackoff nastavení slouží k úpravě pozice prohlížeče tak, že když se klienti soubor a stiskněte tlačítko za provozu edge mohou stále získat fragmenty ze serveru, místo získávání HTTP 404 nebo 412 chyba.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinování více pravidel v jednom filtru
Můžete kombinovat více pravidel filtrování v jednom filtru. Jako příklad můžete definovat pravidla"rozsah" Odebrat slaty z archivu za provozu a také filtrovat dostupné přenosových rychlostí. Při použití více pravidel filtrování, je konečný výsledek průnik všechna pravidla.

![více pravidel][multiple-rules]

## <a name="create-filters-programmatically"></a>Vytvoření filtrů prostřednictvím kódu programu
V následujícím článku popisuje entity Media Services, které souvisí s filtry. Článek také ukazuje, jak programově vytvářet filtry.  

[Vytvoření filtrů rozhraní REST API](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinace více filtrů (složení filtr)
Můžete také kombinovat více filtrů v jednu adresu URL. 

Následující příklad ukazuje, proč můžete chtít zkombinovat filtry:

1. Budete muset filtrovat vaše video vlastnosti pro mobilní zařízení, třeba na Android nebo iPAD (Chcete-li omezit video vlastnosti). Odebrat nežádoucí vlastností, by vytvořit globální filtr vhodný pro profily zařízení. Jak je uvedeno výše v tomto článku, globální filtry lze použít pro všechny prostředky pod stejným účtem služby média bez dalšího přidružení. 
2. Chcete trim počáteční a koncový čas prostředek. Toho dosáhnete tak, by vytvořit místní filtr a nastavit čas zahájení a ukončení. 
3. Chcete kombinovat obě tyto filtry (bez kombinaci, je nutné přidat kvality filtrování oříznutí filtru, což znesnadňuje filtru využití).

Se zkombinovat filtry, budete muset nastavit názvy filtru do manifestu/stop adresu URL s oddělený středníky. Předpokládejme, máte filtr s názvem *MyMobileDevice* , filtry vlastností a druhou s názvem máte *MyStartTime* nastavit konkrétní počáteční čas. Můžete je spojit takto:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Můžete kombinovat až tři filtry. 

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="know-issues-and-limitations"></a>Vědět, problémy a omezení
* Dynamické manifest funguje v GOP hranice (klíč rámce) proto ořezávání má GOP přesnost. 
* Můžete použít stejný název filtru pro místní a globální filtry. Všimněte si, že místní filtr mají vyšší prioritu a přepíše globálních filtrů.
* Pokud aktualizujete filtr, může trvat až 2 minuty koncový bod streamování aktualizovat pravidla. Pokud obsah zpracování pomocí některé filtry (a uložené v mezipaměti v proxy servery a CDN mezipaměti), aktualizace tyto filtry může způsobit selhání přehrávač. Je doporučujeme vymazání mezipaměti po aktualizaci filtru. Pokud tato možnost není možné, zvažte použití jiný filtr.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Doručování obsahu zákazníkům – přehled](media-services-deliver-content-overview.md)

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
