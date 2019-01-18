---
title: Doručování obsahu zákazníkům | Dokumentace Microsoftu
description: Toto téma obsahuje přehled, co je součástí doručování obsahu pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: juliako
ms.openlocfilehash: 3e968c71b87f7227dc4bd2e3f4ee486719b65e40
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388158"
---
# <a name="deliver-content-to-customers"></a>Doručování obsahu zákazníkům
Při dodáváte streamování nebo vod (Video-on-demand) obsah pro zákazníky, je vaším cílem poskytování vysoce kvalitního videa pro různá zařízení v různých síťových podmínkách.

K dosažení tohoto cíle, můžete:

* Kódujte datový proud na datový proud videa s více přenosovými rychlostmi (adaptivní přenosová rychlost). To se postará o kvalitu i síťové podmínky.
* Pomocí Microsoft Azure Media Services [dynamické balení](media-services-dynamic-packaging-overview.md) a dynamicky znovu zabalte datový proud do různých protokolů. Postará se o streamování na různá zařízení. Služba Media Services podporuje doručování následujících adaptivní přenosové rychlosti streamování technologií: <br/>
    * **HTTP Live Streaming** (HLS) - přidat "(formát = m3u8-aapl)" cesty "/ Manifest" část adresy URL říct streamování zdrojový server vrátit zpět obsahu HLS za spotřebu na **Apple iOS** nativní zařízení (Další informace, Zobrazit [lokátory](#locators) a [adresy URL](#URLs)),
    * **MPEG-DASH** -přidat "(format = mpd-time-csf)" cesty "/ Manifest" část adresy URL říct streamování zdrojový server vrátit zpět MPEG-DASH (podrobnosti najdete v tématu [lokátory](#locators) a [adresy URL](#URLs)),
    * **Technologie Smooth Streaming**.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Tento článek obsahuje přehled konceptů důležité doručování obsahu.

Známé problémy, najdete v části [známé problémy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamické balení
Při dynamickém balení této služby Media Services nabízí, že můžete doručovat obsah s adaptivní přenosovou rychlostí kódováním MP4 nebo technologie Smooth Streaming ve formátech streamování podporovaných službou Media Services (MPEG-DASH, HLS, technologie Smooth Streaming) aniž byste museli znovu zabalit do těchto formátů streamování. Doporučujeme, abyste doručování obsahu s dynamickým vytvářením paketů.

Abyste mohli využívat výhody dynamického balení, musíte zakódovat váš soubor mezzanine (zdrojový) do sady souborů MP4 s adaptivní přenosovou rychlostí nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

S dynamickým vytvářením paketů, ukládáte a platíte za soubory v jednom úložném formátu. Služba Media Services bude sestavovat a dodávat odpovídající odpověď podle vašich požadavků.

Dynamické balení je k dispozici pro standard a koncové body streamování premium. 

Další informace najdete v tématu [dynamické balení](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtrů a dynamických manifestů
Můžete definovat filtry pro vaše prostředky pomocí služby Media Services. Tyto filtry se na straně serveru pravidla, které pomůže vašim zákazníkům třeba přehrát určité části videa nebo určete podmnožinu audio a video interpretace, které zařízení vašich zákazníků může zpracovat (ne všechny interpretací, které jsou spojeny s asset). Toto filtrování se dosahuje prostřednictvím *dynamických manifestů* , které vytvářejí, když váš zákazník požaduje pro streamování videa na základě jedné nebo víc zadaných filtrů.

Další informace najdete v tématu [filtrů a dynamických manifestů](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Lokátory
Chcete-li uživatelům poskytnout adresu URL, která slouží ke streamování nebo stažení vašeho obsahu, musíte nejprve publikujte asset tím, že vytvoříte Lokátor. Lokátor představuje vstupní bod pro přístup k souborům obsaženy v assetu. Služba Media Services podporuje dva typy lokátorů:

* Lokátory OnDemandOrigin. Ty se používají ke streamování médií (například MPEG-DASH, HLS nebo technologie Smooth Streaming) nebo progresivně stahovat soubory.
* Lokátory URL sdíleného přístupového podpisu (SAS). Ty se používají ke stahování multimediálních souborů do místního počítače.

*Zásada přístupu* se používá k definování oprávnění (jako jsou čtení, zápisu a seznamu) a doby trvání, pro které má klient přístup pro konkrétní prostředek. Všimněte si, že seznam oprávnění (AccessPermissions.List) by neměl být použité při vytváření Lokátor OnDemandOrigin.

Lokátory mají datum vypršení platnosti. Na webu Azure portal nastaví datum vypršení platnosti 100 let v budoucnosti pro lokátory.

> [!NOTE]
> Pokud jste použili na webu Azure portal k vytvoření lokátorů před březnem 2015, byly tyto lokátory nastavené vyprší za dva roky.
> 
> 

Pokud chcete aktualizovat datum vypršení platnosti lokátoru, použijte rozhraní [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) API nebo [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Upozorňujeme, že při aktualizaci data vypršení platnosti lokátoru SAS se změní adresa URL.

Lokátory nejsou určené ke správě řízení přístupu na uživatele. Pomocí řešení správy digitálních práv (DRM) můžete poskytnout různá přístupová práva pro jednotlivé uživatele. Další informace najdete v tématu [zabezpečení médií](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Když vytvoříte Lokátor, může dojít ke zpoždění 30sekundovém kvůli požadované úložiště a šíření procesy ve službě Azure Storage.

## <a name="adaptive-streaming"></a>Adaptivní streamování
Technologie s adaptivní přenosovou rychlostí povolit aplikací pro přehrávání videa k určení stavu sítě a vybrat z několika přenosových rychlostí. Při komunikaci sítě s zhoršení, že klient může vybrat nižší přenosovou rychlostí, přehrávání může pokračovat v kvalita videa je nižší. Jak vylepšit síťové podmínky, klient může přepnout na vyšší s přenosovou rychlostí s lepší kvalitu videa. Azure Media Services podporuje následující technologie adaptivní přenosové rychlosti: HTTP Live Streaming (HLS), technologie Smooth Streaming a MPEG-DASH.

Uživatelům poskytnout adresy URL pro streamování, nejprve musíte vytvořit lokátor OnDemandOrigin. Vytváření Lokátor obsahuje základní cestu k prostředku, který obsahuje obsah, který chcete Streamovat. Ale abyste mohli Streamovat obsah, musíte upravte tuto cestu další. Pokud chcete vytvořit úplnou adresu URL k souboru manifestu datových proudů, musí zřetězit hodnotu lokátoru cesty a manifest (filename.ism) název souboru. Potom připojte **/Manifest** a příslušném formátu (v případě potřeby) do lokátoru cesty.

> [!NOTE]
> Také můžete Streamovat obsah pomocí připojení SSL. Chcete-li to provést, zkontrolujte, zda že vaší adresy URL pro streamování začínat HTTPS. Všimněte si, že v současné době AMS nepodporuje SSL s použitím vlastních domén.  
> 

Můžete pouze Streamovat přes protokol SSL Pokud koncový bod streamování, ze kterého můžete doručovat obsah byl vytvořen po 10. září 2014. Pokud vaše adresy URL pro streamování jsou založené na koncových bodech streamování vytvořené po 10. září 2014, adresa URL obsahuje "streaming.mediaservices.windows.net." Adresy URL pro streamování, které obsahují "origin.mediaservices.windows.net" (starý formát) nepodporují SSL. Pokud vaše adresa URL používá starý formát a chcete Streamovat přes protokol SSL, vytvořte nový koncový bod streamování. Použití adres URL na základě nového koncového bodu streamování pro streamování vašeho obsahu přes protokol SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Streamování formátech adres URL

### <a name="mpeg-dash-format"></a>Formátu MPEG-DASH
{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formátu Apple HTTP Live Streaming (HLS) V4.
{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formátu Apple HTTP Live Streaming (HLS) V3
{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formátu Apple HTTP Live Streaming (HLS) s filtrem pouze se zvukem
Ve výchozím nastavení, jsou jen zvukové stopy součástí HLS manifestu. To je potřeba Apple Store certifikaci pro mobilní sítě. V takovém případě pokud klient nemá dostatečnou šířku pásma, nebo je připojený prostřednictvím linky 2G připojení, přehrávání se přepne do pouze se zvukem. To pomáhá udržet streamování obsahu bez ukládání do vyrovnávací paměti, ale neexistuje žádná videa. V některých případech může být player ukládání do vyrovnávací paměti upřednostňované nad pouze se zvukem. Pokud chcete odebrat pouze se zvukem sledovat, přidejte **pouze se zvukem = false** na adresu URL.

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3pouze se zvukem = false)

Další informace najdete v tématu [výstupní další funkce podpory dynamické složení manifestu a HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formát technologie Smooth Streaming
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Příklad:

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Technologie Smooth Streaming 2.0 manifestu (starší verze manifestu)
Ve výchozím nastavení technologie Smooth Streaming formát manifestu obsahuje značku opakování (r-tag). Některé přehrávače však nepodporují r-tag. Klienti tyto přehrávačích můžete použít formát, který zakáže r-tag:

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progresivní stahování
Progresivní stahování můžete spustit přehrávání médií předtím, než byly staženy celý soubor. Nelze stáhnout postupně .ism * (ismv isma, ismt soubory nebo ismc).

Pokud chcete obsah progresivně stahovat, použijte typu Lokátor OnDemandOrigin. Následující příklad ukazuje adresu URL, která je založená na typu Lokátor OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Musíte dešifrovat šifrované úložiště prostředky, které chcete Streamovat z původu služby pro progresivní stahování.

## <a name="download"></a>Ke stažení
Pro stažení vašeho obsahu do klientského zařízení, musíte vytvořit lokátor SAS. Lokátor SAS poskytuje přístup ke kontejneru služby Azure Storage ve kterém se nachází váš soubor. Pokud chcete vytvořit adresu URL pro stažení, budete muset vložit název souboru mezi hostitelem a podpis SAS.

Následující příklad ukazuje adresu URL, která je založena na lokátoru SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Platí následující aspekty:

* Musíte dešifrovat šifrované úložiště prostředky, které chcete Streamovat z původu služby pro progresivní stahování.
* Stahování, které se nedokončila během 12 hodin se nezdaří.

## <a name="streaming-endpoints"></a>Koncové body streamování

Koncový bod streamování reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do sítě pro doručování obsahu (CDN) k další distribuci. Výstupní datový proud z datových proudů koncový bod služby může být živý stream nebo vod (Video-on-demand) prostředků ve vašem účtu Media Services. Existují dva typy koncových bodů streamování **standardní** a **premium**. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

## <a name="known-issues"></a>Známé problémy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Verze manifestu změny technologie Smooth Streaming
Před vydáním. července 2016 service – při prostředky pomocí kodéru Media Encoder Standard, pracovní postup kodéru Media Encoder Premium nebo starší kodér médií Azure byly zpracovány pomocí proudu pomocí dynamické balení – technologie Smooth Streaming manifestu vrátila by odpovídat verzi 2.0. Ve verzi 2.0 nepoužívejte dob trvání fragment značky takzvané opakování ("r"). Příklad:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Vydání služby. července 2016 odpovídá vygenerovaný manifest technologie Smooth Streaming na verzi 2.2, s dobami trvání fragment pomocí opakování značek. Příklad:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Některé starší verze klientů, technologie Smooth Streaming nemusí podporovat opakování značky a se nepodaří načíst manifest. Chcete-li tento problém zmírnit, můžete použít parametr starší verze manifestu formátu **(formát = fmp4 v20)** nebo aktualizaci klienta na nejnovější verzi, která podporuje opakování značky. Další informace najdete v tématu [funkce Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Aktualizace lokátory Media Services po postupném zavedení klíče úložiště](media-services-roll-storage-access-keys.md)

