---
title: Filtrů a dynamických manifestů | Dokumentace Microsoftu
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k archivaci selektivní streamování.
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
ms.date: 11/06/2018
ms.author: cenkd;juliako
ms.openlocfilehash: 6060f294820281df3124fb2fc702ece59a006af1
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282403"
---
# <a name="filters-and-dynamic-manifests"></a>Filtrů a dynamických manifestů
Od verze 2.17, Media Services vám umožní definovat filtry pro vaše prostředky. Tyto filtry jsou pravidla na straně serveru, která vám umožní vašim zákazníkům zadáte věci, jako je: přehrávání pouze část videa (namísto přehrání celý video), nebo zadat pouze podmnožinu interpretace zvuku a videa, zařízení vašich zákazníků může zpracovat () Ne všechny interpretace, které jsou přidruženy asset). Toto filtrování vaše prostředky se dosahuje prostřednictvím **dynamické Manifest**, které jsou vytvořeny na vyžádání vašich zákazníků, abyste mohli Streamovat video podle zadané filtry.

Toto téma popisuje běžné scénáře, ve kterém pomocí filtrů by být velmi užitečné pro zákazníky a odkazy na témata, která ukazují, jak programově vytvářet filtry.

## <a name="overview"></a>Přehled
Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vaším cílem je poskytovat vysoce kvalitní videa pro různá zařízení v různých síťových podmínkách. K dosažení tohoto cíle provést následující kroky:

* zakódovat váš datový proud s více přenosovými rychlostmi ([s adaptivní přenosovou rychlostí](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) datový proud videa (to se postará o kvalitu i síťové podmínky) a 
* pomocí Media Services [dynamické balení](media-services-dynamic-packaging-overview.md) a dynamicky znovu zabalte datový proud do různých protokolů (to se postará o streamování na různá zařízení). Služba Media Services podporuje doručování následujících technologií streamování adaptivní přenosové rychlosti: HTTP Live Streaming (HLS), technologie Smooth Streaming a MPEG DASH. 

### <a name="manifest-files"></a>Soubory manifestu
Při kódování prostředku pro streamování s adaptivní přenosovou rychlostí **manifest** se vytvoří soubor (seznam testů) (soubor je založený na textu nebo na základě XML). **Manifest** soubor obsahuje, jako datový proud metadat: sledování typu (zvuk, video nebo text), sledování název, počáteční a koncový čas, s přenosovou rychlostí (Vlastnosti), sledování jazyků, prezentace okno (posuvné okno pevnou platnost), (kodek videa FourCC). Nastaví také přehrávače pro načtení další fragment tím, že poskytuje informace o tyto další možné přehrát video fragmenty dostupné a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" obsah videa.

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

### <a name="dynamic-manifests"></a>Dynamických manifestů
Existují [scénáře](media-services-dynamic-manifest-overview.md#scenarios) když klient potřebuje více flexibility než co je popsána v souboru manifestu výchozí asset. Příklad:

* Konkrétní zařízení: doručování pouze zadaný interpretace nebo zadané jazykové stopy, které podporuje zařízení, která se používá k přehrávání obsahu ("verze filtrování"). 
* Snižte manifest zobrazíte dílčích klipů živé události ("dílčích klipů filtrování").
* Ořízněte úvodní video ("ořezávání video").
* Upravte prezentace okno (DVR) aby bylo možné poskytovat omezenou délku okna DVR v přehrávači ("okno s úpravou prezentace").

K dosažení této flexibility, Media Services nabízí **dynamických manifestů** založené na předem definované [filtry](media-services-dynamic-manifest-overview.md#filters).  Jakmile definujete filtry, klienty je použít k streamování konkrétní verze nebo dílčích klipů vašeho videa. V adrese URL streamování, zadejte filtry. Filtry můžete uplatnit adaptivní přenosové rychlosti streamování protokolů podporovaných [dynamické balení](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH a Smooth Streaming. Příklad:

Adresa URL MPEG DASH s filtrem

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Technologie Smooth Streaming adresa URL s filtrem

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Další informace o tom, jak doručovat obsah a sestavení, adresy URL pro streamování najdete v tématu [doručování obsahu – přehled](media-services-deliver-content-overview.md).

> [!NOTE]
> Všimněte si, že dynamických manifestů neměňte assetu a výchozí manifest pro tento prostředek. Klienta můžete požádat o datový proud s nebo bez filtrů. 
> 
> 

### <a id="filters"></a>Filtry
Existují dva typy filtrů prostředku: 

* Globální filtry (můžete použít u všech prostředků v účtu Azure Media Services, mají životnost účet) a 
* Místní filtry (může používat jedině pro určitý prostředek, pomocí kterého byl tento filtr k při vytvoření, mají životnost asset). 

Globální a místní filtrování typů mají stejné vlastnosti. Hlavní rozdíl mezi nimi je pro scénáře, které je vhodnější jaký typ vyfiltrovat. Globální filtry jsou obecně vhodné pro profily zařízení (interpretace filtrování), kde může použít místní filtry mají být odebrány konkrétní prostředek.

## <a id="scenarios"></a>Obvyklé scénáře
Jak jsem už zmínili, než při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) je vaším cílem poskytování vysoce kvalitního videa pro různá zařízení v různých síťových podmínkách. Kromě toho vaše může mít jiné požadavky, které se týkají filtrování vaše prostředky a použití **dynamické Manifest**s. Následující části poskytují na stručný přehled různých scénářů filtrování.

* Zadejte pouze podmnožinu audio a video interpretace, které určitá zařízení mohou zpracovávat (ne všechny interpretací, které jsou spojeny s asset). 
* Přehrávání pouze část videa (namísto přehrání videa celý).
* Upravte okna DVR prezentace.

## <a name="rendition-filtering"></a>Interpretace filtrování
Můžete kódovat asset více kódování profilů (směrného H.264 plánu, H.264 vysoké, AACL, AACH, Dolby digitální Plus) a více přenosových rychlostí kvality. Profily a přenosových rychlostí všech prostředku se ale nepodporují všechna zařízení klienta. Například starší zařízení se systémem Android podporují pouze H.264 směrného plánu + AACL. Vyšší přenosových rychlostí odesílání do zařízení, které nelze získat výhody, odpad výpočtu šířky pásma a zařízení. Taková zařízení musíte dekódovat všechny zadané informace, jenom na vertikálně snížit kapacitu pro zobrazení.

Díky dynamický Manifest, můžete vytvořit profily zařízení jako jsou mobilní zařízení, konzoly, HD, High Density/SD atd. a zahrnují sleduje a vlastnosti, které mají být součástí každý profil.

![Interpretace filtrování příklad][renditions2]

V následujícím příkladu byl použit pro kodér má kódovat mezzanine asset do sedmi interpretace video soubory MP4 rychlostmi ISO (z 180p 1080p). Zakódovanému assetu můžete dynamicky zabalené do některé z následujících protokolů streamování: MPEG DASH, HLS a Smooth.  V horní části diagramu se zobrazí HLS manifestu pro prostředek s žádné filtry (obsahuje všechny sedm interpretace).  Vlevo dole se zobrazí v manifestu HLS, do které byl použit filtr s názvem "ott". Filtr "ott" Určuje odebrání všech přenosových rychlostí nižší než 1 MB/s, což způsobilo v dolní části dvě úrovně kvality se odstraní v odpovědi. V pravém dolním rohu se zobrazí v manifestu HLS, do které byl použit filtr s názvem "mobilní". "Mobilní" filtr určuje odebrání interpretací, kde je větší než 720p, což způsobilo ve dvou rozlišení 1080p interpretace se odstraní.

![Interpretace filtrování][renditions1]

## <a name="removing-language-tracks"></a>Odebrání jazyka sleduje
Vaše prostředky mohou zahrnovat více zvuku jazyků, jako je angličtina, španělština, francouzština, atd. Obvykle správci Player SDK výchozí výběr zvukové stopy a k dispozici zvuk sleduje podle výběru uživatele. Není lehké vývoj těchto sad SDK Player, vyžaduje jedná o rozdílné implementace na přehrávačů konkrétní zařízení. Na některých platformách také Player rozhraní API jsou omezené a nezahrnují funkce zvuku výběru, kdy uživatelé nemohou vybrat nebo změnit výchozí zvukové stopy. S filtry asset můžete řídit chování tak, že vytvoříte filtry, které obsahují pouze požadované zvuku jazyky.

![Sleduje jazyka filtrování][language_filter]

## <a name="trimming-start-of-an-asset"></a>Oříznutí začátek prostředek
Ve většině živě streamovaných událostí operátoři spustit některé testy před skutečné události. Například může zahrnovat břidlicová takto před začátkem události: "Programu Zbývá: okamžik". Pokud program je archivace, testování a slatu data jsou také archivovat a zahrnuty v prezentaci. Tyto informace však by neměly být uváděny klientům. S dynamický Manifest můžete vytvořit filtr času spuštění a odebrat nežádoucí data z manifestu.

![Oříznutí start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření z živý archív subclips (zobrazení)
Mnoho živé události jsou spuštěna po dlouhou dobu a živý archív může zahrnovat více událostí. Až živá událost skončí, provozovatele vysílání může být vhodné rozdělit živý archív do spuštění programu logické a ukončení sekvence. V dalším kroku publikujte programy v těchto virtuálních samostatně bez příspěvek zpracování živý archív a nevytváří samostatné prostředky (který nezíská výhodou existující fragmenty v mezipaměti CDN). Příklady těchto virtuálních aplikací jsou čtvrtiny football nebo Basketbalový hru, innings v baseballu nebo jednotlivé události každého programu sportu.

S dynamický Manifest můžete vytvořit filtry, pomocí počátečním/koncovém času a vytvořit virtuální zobrazení přes živý archív. 

![Filtr dílčího klipu][subclip_filter]

Filtrované prostředku:

![Lyžování][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Nastavení prezentace okno (DVR)
V současné době Azure Media Services nabízí cyklické archivu, kde je doba trvání nakonfigurovat mezi 5 minutami – 25 hodin. Manifestu filtrování je možné vytvořit postupné okna DVR v horní části archivu, bez odstranění média. Existuje mnoho scénářů, kde chcete poskytnout omezené okna DVR přesunout pomocí živých edge a současně zachovat větší archivační okno vysílání. Provozovatel vysílání vhodné využít data, která je mimo okno DVR zvýrazněte klipy nebo he\she chtít zadat jiný záznam ze systému windows pro různá zařízení. Většina mobilních zařízení například nezpracovávají záznam ze systému windows (okna DVR – 2minutová pro mobilní zařízení a jednu hodinu můžete mít klienty klasické pracovní plochy).

![Okna DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Úprava LiveBackoff (živé pozice)
Filtrování manifestu slouží k odebrání za provozu edge živou program několik sekund. Filtrování umožňuje provozovatele vysílání a podívejte se na prezentaci v bodě publikování ve verzi preview vytvořit oznámení o inzerovaném programu body vložení předtím, než čtenáři příjem datového proudu (zajištěné schválení 30 sekund). Vysílání můžete push těchto oznámení o inzerovaných programech pro jejich klientská rozhraní v čase jim přijatý a zpracovat informace před příležitost oznámení o inzerovaném programu.

Kromě podpory oznámení o inzerovaném programu LiveBackoff nastavení slouží k nastavení pozice prohlížeče tak, aby při klienti odchylek kliknu na okraji za provozu ze serveru místo zobrazování chybu HTTP 404 nebo 412 chyba stále získal fragmenty.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Sloučení více pravidel v jednom filtru
Můžete zkombinovat více pravidel filtrování v jeden filtr. Jako příklad lze definovat pravidlo"rozsah" odebrání živý archív slaty a také vyfiltrovat dostupné přenosových rychlostí. Při použití více pravidel filtrování, konečný výsledek je průsečík všechna pravidla.

![více pravidel][multiple-rules]

## <a name="create-filters-programmatically"></a>Vytváření filtrů prostřednictvím kódu programu
Následující článek popisuje entity Media Services, které se vztahují k filtry. Tento článek také ukazuje, jak programově vytvářet filtry.  

[Vytváření filtrů pomocí rozhraní REST API](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinace více filtrů (složení filtru)
Můžete také kombinovat několik filtrů v jedné adresy URL. 

Následující příklad ukazuje, proč můžete chtít filtry zkombinovat:

1. Potřebujete filtrovat vaše video kvality pro mobilní zařízení, třeba na Android nebo iPAD (aby bylo možné omezit videa kvality). Odebrat nežádoucí kvality, vytvořili byste globálních filtrů vhodný pro profily zařízení. Jak je uvedeno výše v tomto článku, globální filtry je možné pro všechny vaše prostředky v rámci stejného účtu media services bez dalšího přidružení. 
2. Chcete oříznout počáteční a koncový čas prostředku. Za tím účelem by vytvořte místní filtr a nastavte počáteční/koncový čas. 
3. Chcete obě tyto filtry zkombinovat (bez kombinaci, budete muset přidat kvalitní filtrování k oříznutí filtr, což znesnadňuje použití filtru).

Zkombinovat filtry, je nutné nastavit filtr názvů do manifestu/stop URL oddělený středníky. Předpokládejme, že máte filtru s názvem *MyMobileDevice* , která filtruje kvality a druhou s názvem máte *MyStartTime* nastavit konkrétní čas spuštění. Můžete kombinovat takto:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Můžete kombinovat až tři filtry. 

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="know-issues-and-limitations"></a>Vědět, problémy a omezení
* Dynamický manifest funguje v GOP hranice (klíč rámce) proto ořezávání má GOP přesnost. 
* Můžete použít stejný název filtru pro místní a globální filtry. Místní filtry mají vyšší prioritu a přepíše globálních filtrů.
* Pokud aktualizujete filtr, může trvat až 2 minuty, než koncový bod streamování se aktualizovat pravidla. Pokud obsah byl obsluhovat pomocí nějaké filtry (a uložili do mezipaměti v proxy servery a CDN mezipaměti), aktualizuje se tyto filtry může způsobit selhání přehrávače. Doporučuje se vymazání mezipaměti po aktualizaci filtr. Pokud tato možnost není možné, zvažte použití jiného filtru.

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
