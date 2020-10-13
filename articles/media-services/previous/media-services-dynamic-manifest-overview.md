---
title: Filtry a dynamické manifesty | Microsoft Docs
description: V tomto tématu se dozvíte, jak vytvořit filtry, aby je klient mohl používat ke streamování konkrétních oddílů datového proudu. Media Services vytvoří dynamické manifesty pro archivaci tohoto selektivního streamování.
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
ms.openlocfilehash: 2d83b114487f882b7ee38d3d71c84b6abec04a2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266914"
---
# <a name="filters-and-dynamic-manifests"></a>Filtry a dynamické manifesty

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 2](media-services-dynamic-manifest-overview.md)
> * [Verze 3](../latest/filters-dynamic-manifest-overview.md)

Počínaje verzí 2,17 Media Services umožňuje definovat filtry pro vaše prostředky. Tyto filtry jsou pravidla na straně serveru, která zákazníkům umožní vybrat, jestli se mají přehrát jenom část videa (místo přehrávání celého videa), nebo zadat jenom podmnožinu zvukových a video verzí, které zařízení zákazníka může zpracovat (místo všech verzí, které jsou k assetu přidružené). Toto filtrování prostředků se dosahuje prostřednictvím **dynamických manifestů**, které jsou vytvořené na základě žádosti zákazníka o streamování videa na základě zadaných filtrů.

Toto téma popisuje běžné scénáře, v nichž použití filtrů bude pro vaše zákazníky prospěšné a odkazy na témata, která ukazují, jak vytvořit filtry prostřednictvím kódu programu.

## <a name="overview"></a>Přehled
Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) je vaším cílem doručovat vysoce kvalitní video do různých zařízení v různých síťových podmínkách. K dosažení tohoto cíle postupujte takto:

* zakódovat svůj datový proud videa s více přenosovými rychlostmi[(s](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)tím se postará o podmínky kvality a sítě) a 
* pomocí Media Services [dynamického balení](media-services-dynamic-packaging-overview.md) dynamicky znovu zabalit datový proud do různých protokolů (Tato akce zabere na starosti streamování na různých zařízeních). Media Services podporuje doručování následujících technologií pro streamování s adaptivní přenosovou rychlostí: HTTP Live Streaming (HLS), Smooth Streaming a MPEG POMLČKa. 

### <a name="manifest-files"></a>Soubory manifestu
Při kódování assetu pro streamování s adaptivní přenosovou rychlostí se vytvoří soubor **manifestu** (Playlist) (soubor je založený na textu nebo XML). Soubor **manifestu** obsahuje streamovaná metadata, například: typ sledování (zvuk, video nebo text), název stopy, počáteční a koncový čas, přenosová rychlost (kvality), sledovací jazyky, okno prezentace (posuvné okno pevné doby trvání), kodek videa (FourCC). Také instruuje přehrávač, aby načetl další fragment poskytnutím informací o dostupných fragmentech videa o dalším přehrání a jejich umístění. Fragmenty (nebo segmenty) jsou skutečnými "bloky obsahu videa.

Tady je příklad souboru manifestu: 

```xml
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
```

### <a name="dynamic-manifests"></a>Dynamické manifesty
V případě, že váš klient potřebuje větší flexibilitu než to, co je popsáno v souboru manifestu výchozího prostředku, existují [scénáře](media-services-dynamic-manifest-overview.md#scenarios) . Například:

* Specifické pro zařízení: doručovat pouze zadané verze a/nebo zadané stopy jazyka podporované zařízením, které se používá k přehrání obsahu ("filtrování verzí"). 
* Snižte manifest pro zobrazení dílčího klipu živé události ("filtrování dílčích klipů").
* Ořízne začátek videa ("ořezávání videa").
* Upravte okno prezentace (DVR), aby se v přehrávači zajistila omezená délka okna DVR ("Úprava okna prezentace").

Pro dosažení této flexibility Media Services nabízí **dynamické manifesty** založené na předem definovaných [filtrech](media-services-dynamic-manifest-overview.md#filters).  Po definování filtrů je můžou klienti používat ke streamování konkrétní verze nebo dílčích klipů vašeho videa. Budou určovat filtry v adrese URL streamování. Filtry mohou být aplikovány na protokoly streamování s adaptivní přenosovou rychlostí podporovanou [dynamickým balením](media-services-dynamic-packaging-overview.md): HLS, MPEG-spojovník a Smooth Streaming. Například:

Adresa URL POMLČKy MPEG s filtrem

`http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)`

Adresa URL Smooth Streaming s filtrem

`http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)`


Další informace o tom, jak doručovat svůj obsah a vytvořit adresy URL streamování, najdete v tématu [doručování obsahu – přehled](media-services-deliver-content-overview.md).

> [!NOTE]
> Všimněte si, že dynamické manifesty nemění Asset a výchozí manifest tohoto prostředku. Váš klient se může rozhodnout pro vyžádání datového proudu s filtry nebo bez nich. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filtry
Existují dva typy filtrů assetů: 

* Globální filtry (lze použít na jakýkoli Asset v účtu Azure Media Services, mají životnost účtu) a 
* Místní filtry (lze použít pouze na prostředek, ke kterému byl filtr přidružen při vytváření, má životnost prostředku). 

Typy globálních a místních filtrů mají stejné vlastnosti. Hlavní rozdíl mezi těmito dvěma hodnotami je, pro které scénáře, jaký typ souborového je vhodnější. Globální filtry jsou obvykle vhodné pro profily zařízení (filtrování verzí), kde se k oříznutí konkrétního prostředku dají použít místní filtry.

## <a name="common-scenarios"></a><a id="scenarios"></a>Typické scénáře
Jak bylo zmíněno dříve, při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) je vaším cílem doručovat vysoce kvalitní video do různých zařízení v různých síťových podmínkách. Kromě toho mohou být k dispozici další požadavky, které zahrnují filtrování prostředků a používání **dynamického manifestu**s. Následující části poskytují stručný přehled různých scénářů filtrování.

* Zadejte jenom podmnožinu zvukových a video verzí, které některá zařízení můžou zpracovat (místo všech verzí, které jsou k assetu přidružené). 
* Přehrávání pouze části videa (místo přehrávání celého videa).
* Upravit okno prezentace DVR.

## <a name="rendition-filtering"></a>Filtrování verzí
Svůj Asset můžete zakódovat do několika profilů kódování (H. 264 směrný plán, H. 264 vysoké, AACL, AACH, Dolby Digital Plus) a s více přenosovými rychlostmi. Ale ne všechna klientská zařízení budou podporovat všechny profily a přenosové rychlosti vašeho prostředku. Například starší zařízení se systémem Android podporují pouze H. 264 směrného plánu + AACL. Posílání vyšších přenosů do zařízení, které nemůžou získat výhody, zápozici šířky pásma a zařízení. Takové zařízení musí dekódovat všechny dané informace, aby je bylo možné monitorovat.

S dynamickým manifestem můžete vytvořit profily zařízení, jako jsou mobilní, konzola, HD/SD atd., a zahrnout stopy a kvality, které chcete být součástí každého profilu.

![Příklad filtrování verzí][renditions2]

V následujícím příkladu byl kodér použit ke kódování Mezzanine prostředku do sedmi verzí rychlostmi videa ISO (od 180p do 1080p). Kódovaný prostředek se dá dynamicky balit do libovolného z následujících protokolů streamování: HLS, vyhlazení a MPEG POMLČKa.  V horní části diagramu se zobrazí manifest HLS pro Asset bez filtrů (obsahuje všechny sedm verzí).  V levém dolním rohu se zobrazí manifest HLS, na který se použil filtr s názvem "OTT". Filtr "OTT" Určuje odebrání všech přenosů pod 1Mbps, což vedlo k poklesu nejnižší úrovně kvality v reakci. V pravém dolním rohu se zobrazí manifest HLS, na který se použil filtr s názvem "mobilní". Filtr "mobilní" Určuje, že se mají odebrat verze, kde je rozlišení větší než 720p, což vede k vypínání dvou verzí 1080p.

![Filtrování verzí][renditions1]

## <a name="removing-language-tracks"></a>Odebírají se stopy jazyka.
Vaše prostředky můžou zahrnovat několik zvukových jazyků, jako je angličtina, španělština, francouzština atd. Obvykle manažeři sady Player SDK výchozí zvuk sleduje výběr a dostupné zvukové stopy na výběr uživatele. Je obtížné tyto sady SDK pro hráče napravit, ale vyžaduje různé implementace v různých architekturách přehrávače pro konkrétní zařízení. Na některých platformách jsou taky rozhraní API přehrávače omezená a nezahrnují funkci výběru zvuku, kde uživatelé nemůžou vybrat nebo změnit výchozí zvukovou stopu. Pomocí filtrů assetů můžete chování řídit vytvořením filtrů, které obsahují jenom požadované zvukové jazyky.

![Jazyk sleduje filtrování][language_filter]

## <a name="trimming-start-of-an-asset"></a>Zahájení ořezávání assetu
Ve většině událostí živého streamování operátory spouští některé testy před skutečnou událostí. Například mohou zahrnovat jako tuto hodnotu SLAT před začátkem události: "program začne za chvíli". Pokud je program archivován, data testu a SLAT jsou také archivována a součástí prezentace. Tyto informace ale by se neměly zobrazovat klientům. S dynamickým manifestem můžete vytvořit filtr času spuštění a odebrat nežádoucí data z manifestu.

![Začátek ořezávání][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření dílčích klipů (zobrazení) z živého archivu
Spousta živých událostí je dlouhodobě spuštěná a živý archiv může zahrnovat víc událostí. Po skončení živé události budou všesměrové vysílání chtít přerušit živý archiv do sekvence spuštění a zastavení logického programu. Dále tyto virtuální programy publikujte samostatně bez následného zpracování archivu Live Archive a nevytvářejte samostatné prostředky (což nezíská výhodu ze stávajících fragmentů uložených v mezipaměti v sítě CDN). Příklady takových virtuálních programů jsou čtvrtiny fotbalu nebo basketbalový hry, innings v baseballu nebo jednotlivé události jakéhokoli sportovního programu.

S dynamickým manifestem můžete vytvářet filtry pomocí počátečního/koncového času a vytvářet virtuální zobrazení v horní části živého archivu. 

![Filtr dílčích klipů][subclip_filter]

Filtrovaný prostředek:

![Lyžování][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Úprava okna prezentace (DVR)
V současné době Azure Media Services nabízí kruhový archiv, ve kterém je možné nastavit dobu trvání mezi 5 minutami – 25 hodinami. Filtrování manifestu lze použít k vytvoření okna postupného DVR v horní části archivu, aniž byste museli odstraňovat média. Existuje řada scénářů, ve kterých mají všesměrová vysílání poskytnout omezené okno DVR pro přesun s živým okrajem a zároveň zachovat větší archivační interval. Všesměrové vysílání může chtít použít data z okna DVR k zvýraznění klipů nebo může chtít pro různá zařízení poskytnout různá okna se systémem DVR. Například většina mobilních zařízení nezpracovává velká okna ve službě DVR (pro mobilní zařízení a jednu hodinu pro klienty pro stolní počítače můžete mít dvě minuty).

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Úprava LiveBackoff (živá pozice)
Filtrování manifestu lze použít k odebrání několika sekund z živého okraje živého programu. Filtrování umožňuje vysílacím uživatelům sledovat prezentace v bodu publikace Preview a vytvářet reklamní body oznámení, než čtenáři obdrží datový proud (zálohovaný po 30 sekundách). Všesměrové vysílání pak tyto reklamy můžou do svých klientských rozhraní nabízet v čase, aby je mohli přijímat a zpracovávat před tím, než bude reklama v příležitosti.

Kromě podpory inzerce se dá nastavení LiveBackoff použít k nastavení umístění návštěvníků, aby se klienti ztratili a dosáhli živého okraje, ale můžou ze serveru získat fragmenty chyb HTTP 404 nebo 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinování více pravidel v jednom filtru
V jednom filtru můžete zkombinovat více pravidel filtrování. Jako příklad můžete definovat "pravidlo rozsahu" pro odebrání SLAT z živého archivu a také odfiltrovat dostupné přenosové rychlosti. Při použití více pravidel filtrování je konečným výsledkem průnik všech pravidel.

![Vícenásobná pravidla][multiple-rules]

## <a name="create-filters-programmatically"></a>Vytváření filtrů prostřednictvím kódu programu
Následující článek popisuje Media Services entit, které souvisejí s filtry. Tento článek také ukazuje, jak programově vytvářet filtry.  

[Vytvářejte filtry pomocí rozhraní REST API](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinování více filtrů (složení filtru)
V jedné adrese URL můžete také kombinovat více filtrů. 

Následující scénář ukazuje, proč byste mohli chtít kombinovat filtry:

1. Potřebujete filtrovat kvality videa pro mobilní zařízení, jako je Android nebo iPAD (abyste omezili kvality videa). Chcete-li odebrat nechtěné kvality, vytvořte globální filtr vhodný pro profily zařízení. Jak bylo zmíněno dříve v tomto článku, globální filtry lze použít pro všechny vaše prostředky v rámci stejného účtu Media Services bez dalšího přidružení. 
2. Také chcete zkrátit čas zahájení a ukončení assetu. Dosáhnete toho vytvořením místního filtru a nastavením počátečního/koncového času. 
3. Chcete kombinovat oba tyto filtry (bez kombinace, je nutné přidat filtrování kvality do filtru oříznutí, který zjednodušuje použití filtru).

Chcete-li kombinovat filtry, je třeba nastavit názvy filtrů na adresu URL manifestu nebo seznamu stop.%0 s odděleným středníkem. Předpokládejme, že máte filtr s názvem *MyMobileDevice* , který filtruje kvality a máte jinou pojmenovanou *MyStartTime* pro nastavení konkrétního počátečního času. Můžete je kombinovat takto:

`http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)`

Můžete zkombinovat až tři filtry. 

Další informace najdete v [tomto](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="know-issues-and-limitations"></a>Poznejte problémy a omezení
* Dynamický manifest funguje v hranicích skupinu GOP (klíčové snímky), takže ořezávání má skupinu GOPou přesnost. 
* Pro místní a globální filtry můžete použít stejný název filtru. Místní filtry mají vyšší prioritu a přepíšou globální filtry.
* Pokud filtr aktualizujete, může trvat až 2 minuty, než koncový bod streamuje pravidla a aktualizuje pravidla. Pokud byl obsah obsluhován pomocí některých filtrů (a ukládá do mezipaměti proxy a mezipaměti CDN), může při aktualizaci těchto filtrů dojít k selhání přehrávače. Po aktualizaci filtru doporučujeme mezipaměť vymazat. Pokud tato možnost není možná, zvažte použití jiného filtru.

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
