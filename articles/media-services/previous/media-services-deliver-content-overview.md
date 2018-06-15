---
title: Doručování obsahu zákazníkům | Microsoft Docs
description: Toto téma poskytuje přehled o postup při doručování obsahu pomocí služby Azure Media Services.
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
ms.openlocfilehash: 1d1506e26beec3cc48a904ddeb9bbb4e7656a08e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788890"
---
# <a name="deliver-content-to-customers"></a>Doručování obsahu zákazníkům
Pokud jste doručování obsahu streamování na vyžádání, video-on-demand nebo pro zákazníky, vaším cílem je zajistit vysokou kvalitu videa pro různá zařízení v různých síťových podmínkách.

K dosažení tohoto cíle, můžete:

* Kódovat datový proud na datový proud videa s více přenosovými rychlostmi (adaptivní přenosová rychlost). To se postará o kvalitu i síťové podmínky.
* Použít Microsoft Azure Media Services [dynamické balení](media-services-dynamic-packaging-overview.md) a dynamicky znovu zabalte datový proud do různých protokolů. To se postará o streamování na různá zařízení. Služba Media Services podporuje doručování následujících adaptivní přenosové rychlosti streamování technologie: <br/>
    * **HTTP Live Streaming** (HLS) - přidat "(formát = m3u8-aapl)" cesta k "/ Manifest" část adresy URL streamování zdrojového serveru k vrácení zpět HLS obsah pro používání v říct **Apple iOS** nativní zařízení (podrobnosti, v tématu [lokátory](#locators) a [adresy URL](#URLs)),
    * **MPEG-DASH** – přidání "(formát = mpd. čas csf)" cesta k "/ Manifest" část adresy URL říct streamování zdrojový server vrátit zpět MPEG-DASH (podrobnosti najdete v tématu [lokátory](#locators) a [adresy URL](#URLs)),
    * **Technologie Smooth Streaming**.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Tento článek nabízí přehled konceptů důležité doručování obsahu.

Chcete-li zkontrolovat známé problémy, přečtěte si téma [známé problémy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamické balení
Při dynamickém balení této služba Media Services poskytuje, že abyste mohli zajistit obsah s adaptivní přenosovou rychlostí s kódováním MP4 nebo technologie Smooth Streaming ve formátech streamování podporovaných službou Media Services (MPEG-DASH, HLS, technologie Smooth Streaming) bez nutnosti znovu zabalit do těchto formátů streamování. Doporučujeme, abyste doručování obsahu při dynamickém balení.

Pokud chcete využít výhod dynamického balení, zakódovat váš soubor mezzanine (zdrojový) do sady souborů MP4 s adaptivní přenosovou rychlostí nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

Při dynamickém balení, ukládání a platit pro soubory v jednom úložném formátu. Služba Media Services bude sestavovat a dodávat odpovídající reakci na vaše požadavky.

Dynamické balení je k dispozici pro standard a premium koncové body streamování. 

Další informace najdete v tématu [dynamické balení](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtry a dynamické manifestů
Můžete definovat filtry pro vaše prostředky pomocí služby Media Services. Tyto filtry jsou serverové pravidla, které pomáhají zákazníkům provést akce, jako přehrání určitou část videa nebo určete podmnožinu interpretace audia a videa, které může zpracovat vašeho zákazníka zařízení (ne všechny interpretace, které jsou přidruženy asset). Tento filtrování je dosaženo pomocí *dynamické manifesty* který vytvářejí, když váš zákazník požaduje Streamovat videa na základě jedné nebo více určenými filtry.

Další informace najdete v tématu [filtry a dynamické manifesty](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Lokátory
Chcete-li uživateli poskytnout adresu URL, která lze použít ke streamování nebo stažení vašeho obsahu, musíte nejprve publikujte asset tím, že vytvoříte Lokátor. Lokátor představuje vstupní bod pro přístup k souborům obsaženým ve prostředek. Služba Media Services podporuje dva typy lokátorů:

* Lokátory OnDemandOrigin. Ty se používají ke streamování médií (například MPEG-DASH, HLS nebo technologie Smooth Streaming) nebo progresivně stahovat soubory.
* Lokátory URL sdílený přístupový podpis (SAS). Ty se používají ke stahování mediálních souborů do místního počítače.

*Zásada přístupu* se používá k definování oprávnění (například pro čtení, zápisu a seznamu) a doby trvání, pro kterou má klient přístup pro konkrétní prostředek. Všimněte si, že seznam oprávnění (AccessPermissions.List) by neměla být použité při vytváření Lokátor OrDemandOrigin.

Lokátory mají datum vypršení platnosti. Portál Azure nastaví datum vypršení platnosti 100 let v budoucnosti pro lokátory.

> [!NOTE]
> Pokud jste použili portál Azure k vytvoření lokátorů před březnem 2015, byly tyto lokátory nastavené vyprší po dvou letech.
> 
> 

Pokud chcete aktualizovat datum vypršení platnosti lokátoru, použijte rozhraní [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) API nebo [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Upozorňujeme, že při aktualizaci data vypršení platnosti lokátoru SAS se změní adresa URL.

Lokátory nejsou navržená ke správě řízení přístupu na uživatele. Pomocí řešení správy digitálních práv (DRM), můžete zajistit různých přístupová práva k užívání konkrétní uživatelé. Další informace najdete v tématu [zabezpečení média](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Když vytvoříte Lokátor, může být zpoždění 30 sekundu kvůli požadované úložiště a šíření procesy ve službě Azure Storage.

## <a name="adaptive-streaming"></a>Adaptivní streamování
Technologie adaptivní přenosové rychlosti povolit aplikací pro přehrávání videa k určení stavu sítě a vybrat z několika přenosových rychlostí. Když dojde ke zhoršení síťové komunikace, že klient může vybrat nižší přenosovou rychlostí, přehrávání můžete pokračovat s nižší kvalitu videa. Jak zlepšit síťové podmínky, klient přepnout na vyšší přenosovou rychlostí s lepší kvalitu videa. Azure Media Services podporuje následující technologie adaptivní přenosové rychlosti: HTTP Live Streaming (HLS), technologie Smooth Streaming a MPEG-DASH.

Chcete-li uživatelům poskytnout adresy URL pro streamování, nejprve musíte vytvořit lokátor OnDemandOrigin. Vytvořením Lokátor získáte základní cesta pro daný prostředek, který obsahuje obsah, který chcete k vysílání datového proudu. Však být schopni stream tohoto obsahu, musíte upravit další tuto cestu. Chcete-li vytvořit úplnou adresu URL k souboru manifestu streamování, musí řetězení hodnota Lokátor cesty a manifest (filename.ism) název souboru. Pak připojit **/Manifest** a odpovídajícím formátu (v případě potřeby) do lokátoru cesty.

> [!NOTE]
> Můžete také Streamovat obsah pomocí připojení SSL. K tomu, ujistěte se, že vaše streamování adresy URL začínat HTTPS. Všimněte si, že v současné době AMS nepodporuje SSL s vlastní domény.  
> 

Pouze proudy přes protokol SSL Pokud koncový bod streamování, ze kterého doručení obsahu byl vytvořen po 10. září 2014. Pokud vaše streamování adresy URL jsou založené na koncových bodů streamování vytvořený po 10. září 2014, adresa URL obsahuje "streaming.mediaservices.windows.net." Streamování adresy URL, které obsahují "origin.mediaservices.windows.net" (starý formát) nepodporují SSL. Pokud je adresa URL v původním formátu a chcete Streamovat přes protokol SSL, vytvořte nový koncový bod streamování. Pomocí adresy URL založené na nový koncový bod streamování Streamovat obsah přes protokol SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Adresa URL formátů streamování

### <a name="mpeg-dash-format"></a>Formátu MPEG-DASH
{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formát V4 Apple HTTP Live Streaming (HLS)
{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formát Apple HTTP Live Streaming (HLS) V3
{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formát Apple HTTP Live Streaming (HLS) s filtrem jen zvuk
Ve výchozím nastavení, sleduje pouze jsou součástí HLS manifestu. To je potřeba pro certifikaci Apple Store pro mobilní sítě. V takovém případě pokud je klient nemá dostatečnou šířku pásma nebo je připojený prostřednictvím připojení 2G, přehrávání přepne do jen zvukovém souboru. To pomáhá chránit obsah streamování bez ukládání do vyrovnávací paměti, ale žádné video. V některých scénářích může být player ukládání do vyrovnávací paměti upřednostňované přes jen zvukovém souboru. Pokud chcete sledovat pouze odebrat, přidejte **pouze = false** na adresu URL.

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3pouze = false)

Další informace najdete v tématu [dynamické složení Manifest podporu a HLS výstupní další funkce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Funkce Smooth Streaming formátu
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Příklad:

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Technologie Smooth Streaming 2.0 manifest (starší verze manifestu)
Ve výchozím nastavení technologie Smooth Streaming manifestu formátu obsahuje opakování značky (r-tag). Některé přehrávače však nepodporují r-tag. Klienti s tyto přehrávače můžete použít formát, který zakáže značce r:

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progresivní stahování
S progresivní stahování můžete spustit přehrávání média, než byl stažen celý soubor. .Ism * (ismv isma, ismt soubory nebo ismc) nemůže progresivně stahovat.

Chcete-li progresivně stahovat obsah, použijte OnDemandOrigin Typ lokátoru. Následující příklad ukazuje adresu URL, který je založený na typu OnDemandOrigin lokátoru:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Musíte dešifrovat všechny šifrovat úložiště prostředky, které chcete k vysílání datového proudu ze služby původu pro progresivní stahování.

## <a name="download"></a>Ke stažení
Chcete-li stáhnout obsah do klientského zařízení, musíte vytvořit lokátoru SAS. Lokátor SAS umožňuje přístup ke kontejneru úložiště Azure kde je soubor uložený. Pokud chcete vytvořit adresu URL pro stažení, budete muset pro vložení názvu souboru mezi hostitelem a podpis SAS.

Následující příklad ukazuje adresu URL, která je založena na lokátoru SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Platí následující aspekty:

* Musíte dešifrovat všechny šifrovat úložiště prostředky, které chcete k vysílání datového proudu ze služby původu pro progresivní stahování.
* Stažení, který nebyl dokončen v rámci 12 hodin se nezdaří.

## <a name="streaming-endpoints"></a>Koncové body streamování

Koncový bod streamování představuje streamování služba, která může poskytnout obsah přímo do klientské aplikace player nebo k síti pro doručování obsahu (CDN) pro další distribuci. Výstupní datový proud z datových proudů koncový bod služby může být živý datový proud nebo asset videa na přání ve vašem účtu Media Services. Existují dva typy koncových bodů, streamování **standardní** a **premium**. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

## <a name="known-issues"></a>Známé problémy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Změny technologie Smooth Streaming manifest verze
Před vydáním služby července 2016 – když prostředky vyprodukované Media Encoder Standard Media Encoder Premium pracovního postupu nebo dřívější Azure Media Encoder byly streamování pomocí dynamické balení – technologie Smooth Streaming manifest vrátil by odpovídat verze 2.0. Ve verzi 2.0 nepoužívejte doby trvání fragment značky takzvané opakovat (r). Příklad:

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

Ve verzi služby července 2016 vygenerovaný manifest technologie Smooth Streaming vyhovuje verze 2.2, s dobami trvání fragment pomocí opakování značek. Příklad:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Některé starší verze klientů technologie Smooth Streaming nemusí podporovat opakování značky a se nepodaří načíst v manifestu. Pokud chcete tento problém zmírnit, můžete použít parametr starší verze manifestu formátu **(formát = fmp4 v20)** nebo aktualizaci klienta na nejnovější verzi, která podporuje opakování značky. Další informace najdete v tématu [funkce Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Po vrácení klíče úložiště aktualizuje lokátory Media Services](media-services-roll-storage-access-keys.md)

