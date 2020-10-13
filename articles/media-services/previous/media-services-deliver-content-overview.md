---
title: Doručování obsahu zákazníkům
description: Toto téma poskytuje přehled toho, co je součástí doručování obsahu pomocí Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b636d1cdf2e4b9bd137768e22240d8a47d724a97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266030"
---
# <a name="deliver-content-to-customers"></a>Doručování obsahu zákazníkům

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Při doručování obsahu streamování nebo videa na vyžádání zákazníkům je vaším cílem doručovat vysoce kvalitní video do různých zařízení v různých síťových podmínkách.

K dosažení tohoto cíle můžete:

* Zakódovat datový proud na datový proud s více přenosovými rychlostmi (adaptivní přenosové rychlosti) Tím se postará o podmínky kvality a sítě.
* Pomocí Microsoft Azure Media Services [dynamického balení](media-services-dynamic-packaging-overview.md) dynamicky znovu zabalit datový proud do různých protokolů. Tím se postará o streamování na různých zařízeních. Media Services podporuje doručování následujících technologií pro streamování s adaptivní přenosovou rychlostí: <br/>
    * **Http Live Streaming** (HLS) – přidejte cestu "(Format = M3U8-AAPL)" do části adresy URL "/manifest" a sdělte tak serveru zdroje streamování, aby vracel zpět HLS obsah pro spotřebu na nativních zařízeních **Apple iOS** (podrobnosti najdete v tématu [Lokátory](#locators) a [adresy URL](#URLs)).
    * **MPEG-spojovník** -přidat "(Format = MPD-Time-CSF)" cesta k části "/manifest" adresy URL, která upozorní server zdroje streamování, aby vrátil zpět hodnotu MPEG-pomlčka (podrobnosti najdete v tématu [Lokátory](#locators) a [adresy URL](#URLs)),
    * **Smooth Streaming**.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Tento článek obsahuje přehled důležitých konceptů doručování obsahu.

Pokud chcete zjistit známé problémy, přečtěte si téma [známé problémy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamické balení
Díky dynamickému balení, které Media Services poskytuje, můžete doručovat MP4 s adaptivní přenosovou rychlostí nebo Smooth Streaming kódovaný obsah ve formátech streamování, které podporuje Media Services (MPEG-SPOJOVNÍK, HLS, Smooth Streaming,), aniž byste museli znovu zabalit tyto formáty streamování. Doporučujeme doručovat obsah s dynamickým balením.

Pokud chcete využít výhod dynamického balení, musíte soubor Mezzanine (zdrojový soubor) zakódovat do sady souborů MP4 s adaptivní přenosovou rychlostí nebo s adaptivní přenosovou rychlostí Smooth Streaming soubory.

S dynamickým balením ukládáte a platíte soubory v jednom formátu úložiště. Media Services sestaví a zpracuje odpovídající reakci na základě vašich požadavků.

Dynamické balení je k dispozici pro koncové body streamování Standard a Premium. 

Další informace najdete v tématu [dynamické balení](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtry a dynamické manifesty
Můžete definovat filtry pro prostředky pomocí Media Services. Tyto filtry jsou pravidla na straně serveru, která zákazníkům umožňují provádět akce, jako je například přehrávání konkrétní části videa nebo určení podmnožiny zvukových a video verzí, které zařízení zákazníka může zpracovat (místo všech verzí, které jsou k assetu přidružené). Toto filtrování se dosahuje *dynamickými manifesty* , které se vytvoří, když zákazník požaduje streamování videa na základě jednoho nebo více zadaných filtrů.

Další informace najdete v tématu [filtry a dynamické manifesty](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a name="locators"></a>Lokátory
Pokud chcete uživateli poskytnout adresu URL, která se dá použít ke streamování nebo stažení vašeho obsahu, musíte nejdřív publikovat Asset vytvořením lokátoru. Lokátor poskytuje vstupní bod pro přístup k souborům obsaženým v prostředku. Služba Media Services podporuje dva typy lokátorů:

* OnDemandOrigin lokátory. Používají se ke streamování médií (například MPEG-POMLČKy, HLS nebo Smooth Streaming) nebo postupně stahovaných souborů.
* Lokátory adresy URL sdíleného přístupového podpisu (SAS). Slouží ke stažení mediálních souborů do místního počítače.

*Zásady přístupu* slouží k definování oprávnění (například čtení, zápisu a výpis) a doby trvání, ke kterým má klient přístup pro konkrétní prostředek. Všimněte si, že oprávnění list (AccessPermissions. list) by se nemělo používat při vytváření lokátoru OnDemandOrigin.

Lokátory mají datum vypršení platnosti. Azure Portal nastaví datum vypršení platnosti 100 let v budoucnosti pro lokátory.

> [!NOTE]
> Pokud jste použili Azure Portal k vytvoření lokátorů před březen 2015, tyto Lokátory se nastaví tak, aby po dvou letech prošly platností.
> 
> 

Pokud chcete aktualizovat datum vypršení platnosti lokátoru, použijte rozhraní [REST](/rest/api/media/operations/locator#update_a_locator) API nebo [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Upozorňujeme, že při aktualizaci data vypršení platnosti lokátoru SAS se změní adresa URL.

Lokátory nejsou určené ke správě řízení přístupu pro jednotlivé uživatele. Pomocí řešení Digital Rights Management (DRM) můžete jednotlivým uživatelům udělit různá přístupová práva. Další informace najdete v tématu [zabezpečení médií](/previous-versions/azure/dn282272(v=azure.100)).

Při vytváření lokátoru může dojít k prodlevě 30 sekund v důsledku požadovaného úložiště a procesu šíření v Azure Storage.

## <a name="adaptive-streaming"></a>Adaptivní streamování
Technologie adaptivní přenosové rychlosti umožňují aplikacím přehrávače videa určit podmínky sítě a vybírat z několika přenosových rychlostí. Když se síťová komunikace sníží, může klient vybrat nižší přenosovou rychlost, aby přehrávání mohlo pokračovat s nižší kvalitou videa. Když se zlepší stav sítě, klient může přejít na vyšší přenosovou rychlost s lepší kvalitou videa. Azure Media Services podporuje následující technologie adaptivní přenosové rychlosti: HTTP Live Streaming (HLS), Smooth Streaming a MPEG-POMLČKa.

Pokud chcete uživatelům poskytnout adresy URL streamování, musíte nejdřív vytvořit Lokátor OnDemandOrigin. Vytvoření lokátoru vám poskytne základní cestu k assetu, který obsahuje obsah, který chcete streamovat. Abyste ale mohli streamovat tento obsah, musíte tuto cestu dál upravit. Pokud chcete vytvořit úplnou adresu URL souboru manifestu streamování, musíte zřetězit hodnotu cesty lokátoru a název souboru manifestu (filename. ISM). Pak v cestě lokátoru přidejte **/manifest** a odpovídající formát (v případě potřeby).

> [!NOTE]
> Svůj obsah můžete streamovat i přes připojení TLS. Pokud to chcete provést, ujistěte se, že vaše adresy URL streamování začínají na HTTPS. Všimněte si, že v současné době AMS nepodporuje TLS s vlastními doménami.  
> 

Streamování přes TLS můžete odeslat jenom v případě, že koncový bod streamování, ze kterého dodáváte obsah, byl vytvořen po 10. září 2014. Pokud jsou vaše adresy URL streamování založené na koncových bodech streamování vytvořených po 10. září 2014, adresa URL obsahuje "streaming.mediaservices.windows.net". Adresy URL streamování obsahující "origin.mediaservices.windows.net" (starý formát) nepodporují protokol TLS. Pokud je vaše adresa URL ve starém formátu a chcete být schopná streamovat přes TLS, vytvořte nový koncový bod streamování. Použijte adresy URL na základě nového koncového bodu streamování pro streamování obsahu přes TLS.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Formáty adresy URL pro streamování

### <a name="mpeg-dash-format"></a>Formát pro MPEG-SPOJOVNÍK
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = MPD-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formát Apple HTTP Live Streaming (HLS) v4
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formát Apple HTTP Live Streaming (HLS) V3
{streamování názvu koncového bodu – název účtu Media Services}. streamování. MediaServices. Windows. NET/{Locator ID}/{filename}.ism/Manifest (Format = M3U8-AAPL-V3)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL-V3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formát Apple HTTP Live Streaming (HLS) s filtrem pouze zvuku
Ve výchozím nastavení jsou v manifestu HLS zahrnuty pouze zvukové stopy. To je vyžadováno pro certifikaci Apple Store pro mobilní sítě. V takovém případě, pokud klient nemá dostatečnou šířku pásma nebo je připojený přes připojení 2G, přehrávání přepíná jenom na zvuk. To pomáhá udržet streamování obsahu bez ukládání do vyrovnávací paměti, ale nejedná se o video. V některých scénářích se ukládání do vyrovnávací paměti přehrávače může upřednostnit jenom přes zvuk. Pokud chcete zvuk, který chcete sledovat, odebrat jenom **zvuk = false** , přidejte ho k adrese URL.

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL-V3; pouze audio = false)

Další informace najdete v tématu [Podpora dynamického skládání manifestu a HLS výstup dalších funkcí](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formát Smooth Streaming
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Příklad:

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Manifest Smooth Streaming 2,0 (starší manifest)
Ve výchozím nastavení obsahuje Smooth Streaming formát manifestu značku opakování (značka r). Někteří hráči ale nepodporují značku r. Klienti s těmito hráči můžou používat formát, který zakazuje značku r:

{streamování názvu koncového bodu – název účtu Media Services}. streamování. MediaServices. Windows. NET/{Locator ID}/{filename}.ism/Manifest (Format = FMP4-V20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Progresivní stahování
Při progresivním stahování můžete začít přehrávat média před stažením celého souboru. Nemůžete postupně stahovat soubory. ISM * (ISMV, ISMA, ismt nebo ISMC).

Chcete-li postupně stahovat obsah, použijte OnDemandOrigin typ lokátoru. Následující příklad ukazuje adresu URL, která je založena na OnDemandOrigin typu lokátoru:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

Je nutné dešifrovat všechny prostředky šifrované v úložišti, které chcete streamovat ze služby Origin Service pro progresivní stahování.

## <a name="download"></a>Stáhnout
Chcete-li stáhnout obsah do klientského zařízení, je nutné vytvořit Lokátor SAS. Lokátor SAS vám umožní přístup k kontejneru Azure Storage, ve kterém se soubor nachází. Chcete-li vytvořit adresu URL pro stahování, je nutné vložit název souboru mezi hostitele a podpis SAS.

Následující příklad ukazuje adresu URL, která je založena na lokátoru SAS:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

Platí následující důležité informace:

* Je nutné dešifrovat všechny prostředky šifrované v úložišti, které chcete streamovat ze služby Origin Service pro progresivní stahování.
* Stažení, které nebylo dokončeno během 12 hodin, se nezdaří.

## <a name="streaming-endpoints"></a>Koncové body streamování

Koncový bod streamování představuje službu streamování, která může doručovat obsah přímo do aplikace klienta z přehrávače nebo do sítě pro doručování obsahu (CDN) pro další distribuci. Odchozí datový proud ze služby streamování koncového bodu streamování může být v účtu Media Services živým datovým proudem nebo prostředkem videa na vyžádání. Existují dva typy koncových bodů streamování, **Standard** a **Premium**. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

## <a name="known-issues"></a>Známé problémy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Změny verze manifestu Smooth Streaming
Před vydáním služby z července 2016 – když byly assety vytvořené pomocí Media Encoder Standard, Media Encoder Premium Workflow nebo dřívější Azure Media Encoder streamované pomocí dynamického balení – vrácený Smooth Streamingový manifest by odpovídal verzi 2,0. Ve verzi 2,0 nejsou fragmenty trvání fragmentů použity jako značky opakování (' r '). Například:

```xml
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
```

Ve vydání z července 2016 se vygenerovaný manifest Smooth Streaming v souladu s verzí 2,2 s dobami fragmentace pomocí značek opakování. Například:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Někteří starší klienti Smooth Streaming nemusí podporovat opakované značky a načtení manifestu se nezdaří. Pro zmírnění tohoto problému můžete použít starý parametr formátu manifestu **(Format = FMP4-V20)** nebo aktualizovat klienta na nejnovější verzi, která podporuje značky opakování. Další informace najdete v tématu [Smooth Streaming 2,0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Po navracení klíčů úložiště aktualizovat Lokátory Media Services](media-services-roll-storage-access-keys.md)
