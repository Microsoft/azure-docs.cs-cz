---
title: Filtry a Azure Media Services dynamických manifestů | Dokumentace Microsoftu
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k selektivní Streamovat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 72466906e77c5a62e1dfc5cf8af2869e967c69c0
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339920"
---
# <a name="dynamic-manifests"></a>Dynamických manifestů

Media Services nabízí **dynamických manifestů** na základě předdefinovaných filtrů. Jakmile definujete filtry (naleznete v tématu [definovat filtry](filters-concept.md)), klienty je použít ke streamování konkrétní verze nebo dílčích klipů vašeho videa. V adrese URL streamování, zadejte filtry. Filtry můžete uplatnit adaptivní přenosové rychlosti streamování protokolů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming. 

V následující tabulce jsou uvedeny příklady adresy URL s filtry:

|Protocol (Protokol)|Příklad:|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|


> [!NOTE]
> Dynamický manifest neměňte assetu a výchozí manifest pro tento prostředek. Klienta můžete požádat o datový proud s nebo bez filtrů. 
> 

Toto téma vysvětluje koncepty související s **dynamických manifestů** a uvádí příklady scénářů, ve kterých může být vhodné tuto funkci používat.

## <a name="manifest-files-overview"></a>Přehled soubory manifestu

Při kódování prostředku pro streamování s adaptivní přenosovou rychlostí **manifest** se vytvoří soubor (seznam testů) (soubor je založený na textu nebo na základě XML). **Manifest** soubor obsahuje, jako datový proud metadat: sledování typu (zvuk, video nebo text), sledování název, počáteční a koncový čas, s přenosovou rychlostí (Vlastnosti), sledování jazyků, prezentace okno (posuvné okno pevnou platnost), (kodek videa FourCC). Nastaví také přehrávače pro načtení další fragment tím, že poskytuje informace o tyto další možné přehrát video fragmenty dostupné a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" obsah videa.

Tady je příklad souboru manifestu HLS: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>Získání a zkontrolovat soubory manifestu

Můžete zadat seznam podmínek vlastností sledování filtru podle, na kterém sleduje váš datový proud (Live nebo Video na vyžádání) by měly být zahrnuty do dynamicky generovaný manifest. Pokud chcete získat a podívejte se na vlastnosti drah, musíte nejdřív načíst manifest technologie Smooth Streaming.

[Nahrávání, kódování a streamování souborů s využitím .NET](stream-files-tutorial-with-api.md) kurzu se dozvíte, jak sestavit adresy URL pro streamování s využitím .NET (zobrazíte [vytváření adres URL](stream-files-tutorial-with-api.md#get-streaming-urls) části). Pokud spustíte aplikaci, jedna z adres URL odkazuje na manifest technologie Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Zkopírujte a vložte adresu URL do adresního řádku prohlížeče. Soubor se stáhne. Lze jej otevřít v textovém editoru podle vašeho výběru.

Příklad REST naleznete v tématu [nahrávání, kódování a streamování souborů s využitím REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorování s přenosovou rychlostí streamu videa

Můžete použít [stránky ukázku Azure Media Player](http://aka.ms/amp) monitorování přenosové rychlosti datový proud videa. Ukázka stránka zobrazuje diagnostické informace v **diagnostiky** kartu:

![Diagnostika Azure Media Player][amp_diagnostics]

## <a name="rendition-filtering"></a>Interpretace filtrování

Můžete kódovat asset více kódování profilů (směrného H.264 plánu, H.264 vysoké, AACL, AACH, Dolby digitální Plus) a více přenosových rychlostí kvality. Profily a přenosových rychlostí všech prostředku se ale nepodporují všechna zařízení klienta. Například starší zařízení se systémem Android podporují pouze H.264 směrného plánu + AACL. Odesílání vyšší přenosových rychlostí na zařízení, které nelze získat výhody, plýtvá šířkou pásma a výpočet zařízení. Taková zařízení musíte dekódovat všechny zadané informace, jenom na vertikálně snížit kapacitu pro zobrazení.

Díky dynamický Manifest, můžete vytvořit profily zařízení jako jsou mobilní zařízení, konzoly, HD, High Density/SD atd. a zahrnují sleduje a vlastnosti, které mají být součástí každý profil.

![Interpretace filtrování příklad][renditions2]

V následujícím příkladu byl použit pro kodér má kódovat mezzanine asset do sedmi interpretace video soubory MP4 rychlostmi ISO (z 180p 1080p). Zakódovanému assetu můžete dynamicky zabalené do některé z následujících protokolů streamování: HLS, MPEG DASH a Smooth.  V horní části diagramu se zobrazí HLS manifestu pro prostředek s žádné filtry (obsahuje všechny sedm interpretace).  Vlevo dole se zobrazí v manifestu HLS, do které byl použit filtr s názvem "ott". Filtr "ott" Určuje odebrání všech přenosových rychlostí nižší než 1 MB/s, což způsobilo v dolní části dvě úrovně kvality se odstraní v odpovědi. V pravém dolním rohu se zobrazí v manifestu HLS, do které byl použit filtr s názvem "mobilní". "Mobilní" filtr určuje odebrání interpretací, kde je větší než 720p, což způsobilo ve dvou rozlišení 1080p interpretace se odstraní.

![Interpretace filtrování][renditions1]

## <a name="removing-language-tracks"></a>Odebrání jazyka sleduje
Vaše prostředky mohou zahrnovat více zvuku jazyků, jako je angličtina, španělština, francouzština, atd. Obvykle správci Player SDK výchozí výběr zvukové stopy a k dispozici zvuk sleduje podle výběru uživatele. Není lehké vývoj těchto sad SDK Player, vyžaduje jedná o rozdílné implementace na přehrávačů konkrétní zařízení. Na některých platformách také Player rozhraní API jsou omezené a nezahrnují funkce zvuku výběru, kdy uživatelé nemohou vybrat nebo změnit výchozí zvukové stopy. S filtry asset můžete řídit chování tak, že vytvoříte filtry, které obsahují pouze požadované zvuku jazyky.

![Sleduje jazyka filtrování][language_filter]

## <a name="trimming-start-of-an-asset"></a>Oříznutí začátek prostředek
Ve většině živě streamovaných událostí operátoři spustit některé testy před skutečné události. Například může patří mezi ně břidlicová takto před začátkem události: "Programu Zbývá: okamžik". Pokud program je archivace, testování a slatu data jsou také archivovat a zahrnuty v prezentaci. Tyto informace však by neměly být uváděny klientům. S dynamický Manifest můžete vytvořit filtr času spuštění a odebrat nežádoucí data z manifestu.

![Oříznutí start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Vytváření z živý archív subclips (zobrazení)
Mnoho živé události jsou spuštěna po dlouhou dobu a živý archív může zahrnovat více událostí. Až živá událost skončí, provozovatele vysílání může být vhodné rozdělit živý archív do spuštění programu logické a ukončení sekvence. V dalším kroku publikujte programy v těchto virtuálních samostatně bez příspěvek zpracování živý archív a nevytváří samostatné prostředky (který nezíská výhodou existující fragmenty v mezipaměti CDN). Příklady těchto virtuálních aplikací jsou čtvrtiny football nebo Basketbalový hru, innings v baseballu nebo jednotlivé události každého programu sportu.

S dynamický Manifest můžete vytvořit filtry, pomocí počátečním/koncovém času a vytvořit virtuální zobrazení přes živý archív. 

![Filtr dílčího klipu][subclip_filter]

Filtrované prostředku:

![Lyžování][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Nastavení prezentace okno (DVR)
V současné době Azure Media Services nabízí cyklické archivu, kde je doba trvání nakonfigurovat mezi 5 minutami – 25 hodin. Manifestu filtrování je možné vytvořit postupné okna DVR v horní části archivu, bez odstranění média. Existuje mnoho scénářů, kde chcete poskytnout omezené okna DVR přesunout pomocí živých edge a současně zachovat větší archivační okno vysílání. Provozovatel vysílání vhodné využít data, která je mimo okno DVR zvýrazněte klipy nebo může být vhodné poskytnout jiný záznam ze systému windows pro různá zařízení. Většina mobilních zařízení například nezpracovávají záznam ze systému windows (okna DVR – 2minutová pro mobilní zařízení a jednu hodinu můžete mít klienty klasické pracovní plochy).

![Okna DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Úprava LiveBackoff (živé pozice)
Filtrování manifestu slouží k odebrání za provozu edge živou program několik sekund. Filtrování umožňuje provozovatele vysílání a podívejte se na prezentaci v bodě publikování ve verzi preview vytvořit oznámení o inzerovaném programu body vložení předtím, než čtenáři příjem datového proudu (zajištěné schválení 30 sekund). Vysílání můžete push těchto oznámení o inzerovaných programech pro jejich klientská rozhraní v čase jim přijatý a zpracovat informace před příležitost oznámení o inzerovaném programu.

Kromě podpory oznámení o inzerovaném programu LiveBackoff nastavení slouží k nastavení pozice prohlížeče tak, aby při klienti odchylek kliknu na okraji za provozu ze serveru místo zobrazování chybu HTTP 404 nebo 412 chyba stále získal fragmenty.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Sloučení více pravidel v jednom filtru
Můžete zkombinovat více pravidel filtrování v jeden filtr. Jako příklad lze definovat pravidlo"rozsah" odebrání živý archív slaty a také vyfiltrovat dostupné přenosových rychlostí. Při použití více pravidel filtrování, konečný výsledek je průsečík všechna pravidla.

![více pravidel][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinace více filtrů (složení filtru)

Můžete také kombinovat několik filtrů v jedné adresy URL. 

Následující příklad ukazuje, proč můžete chtít filtry zkombinovat:

1. Potřebujete filtrovat vaše video kvality pro mobilní zařízení, třeba na Android nebo iPAD (aby bylo možné omezit videa kvality). Pokud chcete odebrat nežádoucí kvality, byste měli vytvořit filtr účet vhodný pro profily zařízení. Filtry účtů lze použít pro všechny vaše prostředky v rámci stejného účtu media services bez dalšího přidružení. 
2. Chcete oříznout počáteční a koncový čas prostředku. Za tím účelem by vytvořte filtr Asset a nastavte počáteční/koncový čas. 
3. Chcete obě tyto filtry zkombinovat (bez kombinaci, budete muset přidat kvalitní filtrování k oříznutí filtr, což znesnadňuje použití filtru).

Zkombinovat filtry, je nutné nastavit filtr názvů do manifestu/stop URL oddělený středníky. Předpokládejme, že máte filtru s názvem *MyMobileDevice* , která filtruje kvality a druhou s názvem máte *MyStartTime* nastavit konkrétní čas spuštění. Můžete kombinovat takto:

Můžete kombinovat až tři filtry. 

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogu.

## <a name="considerations-and-limitations"></a>Požadavky a omezení

- Hodnoty pro **forceEndTimestamp**, **presentationWindowDuration**, a **liveBackoffDuration** by se neměla nastavovat pro filtr videa na vyžádání. Používají se jenom pro scénáře živé filtru. 
- Dynamický manifest funguje v GOP hranice (klíč rámce) proto ořezávání má GOP přesnost. 
- Můžete použít stejný název filtru pro účet a Asset filtry. Asset filtry mají vyšší prioritu a přepíše filtry účtů.
- Pokud aktualizujete filtr, může trvat až 2 minuty, než koncový bod streamování se aktualizovat pravidla. Pokud obsah byl obsluhovat pomocí nějaké filtry (a uložili do mezipaměti v proxy servery a CDN mezipaměti), aktualizuje se tyto filtry může způsobit selhání přehrávače. Doporučuje se vymazání mezipaměti po aktualizaci filtr. Pokud tato možnost není možné, zvažte použití jiného filtru.
- Zákazníci musí ručně stáhněte manifest a analyzovat přesné startTimestamp a časové měřítko.
    
    - Chcete-li zjistit vlastnosti v prostředku, která sleduje [získat a zkontrolujte soubor manifestu](#get-and-examine-manifest-files).
    - Vzorec, který chcete nastavit filtr asset vlastnosti časového razítka: <br/>startTimestamp = &lt;čas spuštění v manifestu&gt; +  &lt;očekávaný čas zahájení filtru v řádu sekund&gt;* časové osy


## <a name="next-steps"></a>Další postup

Následující články popisují, jak programově vytvářet filtry.  

- [Vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
