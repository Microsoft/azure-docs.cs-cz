---
title: Doručování obsahu zákazníkům | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled o tom, co se podílí na doručování obsahu pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c8d32a6434db0fad18b9fe7c2d6e2117795eb651
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476737"
---
# <a name="deliver-content-to-customers"></a>Doručování obsahu zákazníkům
Když zákazníkům dodáváte obsah streamování nebo videa na vyžádání, vaším cílem je dodávat vysoce kvalitní video do různých zařízení za různých síťových podmínek.

K dosažení tohoto cíle můžete:

* Zakódujte datový proud do datového toku s více přenosovým tokem (adaptivní datový tok). Tím se postaráme o kvalitu a síťové podmínky.
* Dynamické [balení](media-services-dynamic-packaging-overview.md) Microsoft Azure Media Services můžete dynamicky znovu zabalit datový proud do různých protokolů. Tím se postaráte o streamování na různých zařízeních. Služba Media Services podporuje poskytování následujících technologií adaptivního datového toku: <br/>
    * **HTTP Live Streaming** (HLS) - přidat "(format=m3u8-aapl)" cestu k "/Manifest" část URL říct streaming původu serveru vrátit zpět HLS obsah pro spotřebu na **Apple iOS** nativní zařízení (podrobnosti, viz [lokátory](#locators) a [ADRESY URL](#URLs)),
    * **MPEG-DASH** - přidat "(format=mpd-time-csf)" cestu k "/Manifest" část URL říct streaming původu serveru vrátit zpět MPEG-DASH (podrobnosti, viz [lokátory](#locators) a [adresy URL](#URLs)),
    * **Plynulé streamování**.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Tento článek poskytuje přehled důležitých konceptů doručování obsahu.

Chcete-li zkontrolovat známé problémy, naleznete [v tématu Známé problémy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamické balení
Díky dynamickému balení, které poskytuje mediální služby, můžete poskytovat obsah kódovaný pro funkce MP4 nebo plynulé streamování s adaptivním datovým tokem ve formátech datových proudů podporovaných službou Media Services (MPEG-DASH, HLS, Smooth Streaming), aniž byste museli znovu zabalit do těchto formátů streamování. Doporučujeme dodávat váš obsah s dynamickým balením.

Chcete-li využít výhod dynamického balení, musíte kódovat mezipatro (zdrojový) soubor do sady souborů MP4 s adaptivní matourychlostí nebo adaptivního datového toku Smooth Streaming.

S dynamickým balením ukládáte a platíte za soubory v jediném formátu úložiště. Mediální služby vytvoří a budou poskytovat odpovídající odpověď na základě vašich požadavků.

Dynamické balení je k dispozici pro standardní a prémiové koncové body streamování. 

Další informace naleznete v tématu [Dynamické balení](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtry a dynamické manifesty
Pomocí služby Media Services můžete definovat filtry pro své datové zdroje. Tyto filtry jsou pravidla na straně serveru, která pomáhají zákazníkům například přehrávat určitou část videa nebo určit podmnožinu zvukových a obrazových interpretací, které zařízení zákazníka zvládne (namísto všech interpretací, které jsou přidruženy k datovému zdroji). Tohoto filtrování je dosaženo prostřednictvím *dynamických manifestů,* které jsou vytvořeny, když zákazník požádá o streamování videa na základě jednoho nebo více zadaných filtrů.

Další informace naleznete v [tématu Filtry a dynamické manifesty](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a id="locators"/>Lokátory
Chcete-li uživateli poskytnout adresu URL, kterou lze použít k streamování nebo stahování obsahu, musíte nejprve publikovat datový zdroj vytvořením lokátoru. Lokátor poskytuje vstupní bod pro přístup k souborům obsaženým v datovém zdroji. Služba Media Services podporuje dva typy lokátorů:

* OnDemandOrigin lokátory. Ty se používají k vysílání datových proudů médií (například MPEG-DASH, HLS nebo Smooth Streaming) nebo k postupnému stahování souborů.
* Lokátory adres URL sdíleného přístupového podpisu (SAS). Slouží ke stažení mediálních souborů do místního počítače.

*Zásady přístupu* se používají k definování oprávnění (například čtení, zápisu a seznamu) a doby trvání, pro kterou má klient přístup k určitému prostředku. Všimněte si, že oprávnění seznamu (AccessPermissions.List) by neměl být použit při vytváření lokátoru OnDemandOrigin.

Lokátory mají data vypršení platnosti. Portál Azure nastavuje datum vypršení platnosti 100 let v budoucnu pro lokátory.

> [!NOTE]
> Pokud jste použili portál Azure k vytvoření lokátorů před březnem 2015, tyto lokátory byly nastaveny na vypršení platnosti po dvou letech.
> 
> 

Pokud chcete aktualizovat datum vypršení platnosti lokátoru, použijte rozhraní [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) API nebo [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Upozorňujeme, že při aktualizaci data vypršení platnosti lokátoru SAS se změní adresa URL.

Lokátory nejsou určeny ke správě řízení přístupu pro jednotlivé uživatele. Pomocí řešení DRM (Digital Rights Management) můžete jednotlivým uživatelům udělit různá přístupová práva. Další informace naleznete v tématu [Zabezpečení médií](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Když vytvoříte lokátor, může dojít k 30sekundovému zpoždění z důvodu požadovaných procesů úložiště a šíření ve službě Azure Storage.

## <a name="adaptive-streaming"></a>Adaptivní streamování
Technologie adaptivního přenosového toku umožňují aplikacím přehrávače videa určit podmínky v síti a vybrat z několika datových tok. Pokud se síťová komunikace sníží, klient může vybrat nižší přenosovou rychlost, takže přehrávání může pokračovat s nižší kvalitou videa. Jak se zlepšují podmínky v síti, klient může přepnout na vyšší přenosový tok s vylepšenou kvalitou videa. Azure Media Services podporuje následující technologie adaptivního datového toku: HTTP Live Streaming (HLS), Smooth Streaming a MPEG-DASH.

Chcete-li uživatelům poskytnout streamované adresy URL, musíte nejprve vytvořit lokátor OnDemandOrigin. Vytvoření lokátoru vám poskytne základní cestu k datovému zdroji, který obsahuje obsah, který chcete streamovat. Chcete-li však tento obsah streamovat, je třeba tuto cestu dále upravit. Chcete-li vytvořit úplnou adresu URL souboru manifestu streamování, musíte zřetězit hodnotu cesty lokátoru a název souboru manifestu (filename.ism). Potom přidejte **/Manifest** a vhodný formát (v případě potřeby) k cestě lokátoru.

> [!NOTE]
> Můžete také streamovat obsah přes připojení TLS. Chcete-li to provést, ujistěte se, že vaše streamované adresy URL začínají protokolem HTTPS. Všimněte si, že v současné době AMS nepodporuje TLS s vlastními doménami.  
> 

Streamovat přes TLS lze pouze v případě, že koncový bod streamování, ze kterého doručujete obsah byl vytvořen po 10 září 2014. Pokud jsou vaše adresy URL streamování založeny na koncových bodech streamování vytvořených po 10 streaming.mediaservices.windows.net. Datové adresy URL datových proudů, které obsahují "origin.mediaservices.windows.net" (starý formát), nepodporují tls. Pokud je vaše adresa URL ve starém formátu a chcete mít možnost datového proudu přes TLS, vytvořte nový koncový bod streamování. Pomocí adres URL založených na novém koncovém bodu streamování můžete streamovat obsah přes TLS.

## <a name="streaming-url-formats"></a><a id="URLs"/>Formáty adres URL pro streamování

### <a name="mpeg-dash-format"></a>Formát MPEG-DASH
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formát V4 Apple HTTP Live Streaming (HLS)
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formát Apple HTTP Live Streaming (HLS) V3
{název_mediálního serveru koncového bodu název účtu}.streaming.mediaservices.windows.net/{locator ID}/{název_souboru}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formát Apple HTTP Live Streaming (HLS) s filtrem pouze pro zvuk
Ve výchozím nastavení jsou stopy pouze pro zvuk zahrnuty do manifestu HLS. To je nutné pro certifikaci Apple Store pro mobilní sítě. V tomto případě, pokud klient nemá dostatečnou šířku pásma nebo je připojen přes připojení 2G, přehrávání se přepne pouze na zvuk. To pomáhá udržovat streamování obsahu bez ukládání do vyrovnávací paměti, ale neexistuje žádné video. V některých případech může být ukládání do vyrovnávací paměti přehrávače upřednostňováno pouze před zvukem. Pokud chcete odebrat pouze zvukovou stopu, přidejte do adresy URL **audio-only=false.**

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Další informace naleznete [v tématu Podpora dynamického manifestu a další funkce výstupu HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formát plynulého streamování
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Příklad:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Manifest Plynulé streamování 2.0 (starší manifest)
Ve výchozím nastavení obsahuje formát manifestu plynulé hodování značku opakování (značka r-tag). Někteří hráči však značku r nepodporují. Klienti s těmito přehrávači mohou použít formát, který zakáže značku r:

{název_mediálního serveru koncového bodu název účtu}.streaming.mediaservices.windows.net/{locator ID}/{název_souboru}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Postupné stahování
S postupným stahováním můžete začít přehrávat média před stažením celého souboru. Soubory ISM* (ismv, isma, ismt nebo ismc) nelze postupně stahovat.

Chcete-li postupně stahovat obsah, použijte ondemandorigin typ lokátoru. Následující příklad ukazuje adresu URL, která je založena na ondemandorigin typ lokátoru:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Je nutné dešifrovat všechny prostředky šifrované úložištěm, které chcete streamovat ze služby původu pro postupné stahování.

## <a name="download"></a>Stáhnout
Chcete-li stáhnout obsah do klientského zařízení, musíte vytvořit lokátor SAS. Lokátor SAS umožňuje přístup ke kontejneru úložiště Azure, kde se nachází váš soubor. Chcete-li vytvořit adresu URL pro stažení, musíte vložit název souboru mezi hostitele a podpis SAS.

Následující příklad ukazuje adresu URL, která je založena na lokátoru SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Platí následující důležité informace:

* Je nutné dešifrovat všechny prostředky šifrované úložištěm, které chcete streamovat ze služby původu pro postupné stahování.
* Stahování, které nebylo dokončeno do 12 hodin, se nezdaří.

## <a name="streaming-endpoints"></a>Koncové body streamování

Koncový bod streamování představuje službu streamování, která může doručovat obsah přímo do aplikace přehrávače klienta nebo do sítě pro doručování obsahu (CDN) pro další distribuci. Odchozí datový proud ze služby koncového bodu streamování může být živý datový proud nebo datový zdroj videa na vyžádání ve vašem účtu Media Services. Existují dva typy koncových bodů streamování, **standardní** a **prémiové**. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

## <a name="known-issues"></a>Známé problémy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Změny verze manifestu Plynulé hodovací hod
Před vydáním služby v červenci 2016 – když byly datové zdroje vytvořené standardem Media Encoder Standard, Media Encoder Premium Workflow nebo starším kodéru médií Azure Encoder streamovány pomocí dynamického balení – manifest Hladký streamování, který by byl vrácen, by odpovídal verzi 2.0. Ve verzi 2.0 nepoužívají doby trvání fragmentu tzv. Například:


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

Ve verzi služby z července 2016 se vygenerovaný manifest vyhlazení datových proudů shoduje s verzí 2.2 s dobou trvání fragmentu pomocí značek opakování. Například:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Některé starší klienty hladkého streamování nemusí podporovat opakované značky a nepodaří se načíst manifest. Chcete-li tento problém zmírnit, můžete použít starší parametr formátu manifestu **(format=fmp4-v20)** nebo aktualizovat klienta na nejnovější verzi, která podporuje značky opakování. Další informace naleznete [v tématu Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Aktualizace lokátorů mediálních služeb po vrácení klíčů úložiště](media-services-roll-storage-access-keys.md)

