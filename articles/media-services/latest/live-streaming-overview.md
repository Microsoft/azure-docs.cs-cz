---
title: Přehled živého streamování využívajícího službu Azure Media Services | Dokumentace Microsoftu
description: Tento článek poskytuje přehled živého streamování využívajícího službu Azure Media Services v3.
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
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 91e24fb274c1f9895046e8e2e7d760d02d196ccd
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354174"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování pomocí služby Azure Media Services v3

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Ke streamování živých událostí pomocí služby Media Services, budete potřebovat následující:  

- Fotoaparát, který se používá k zachycení živé události.
- Živé video encoder, který převádí signály z kamery (nebo jiné zařízení, jako je přenosný počítač) příspěvek informačního kanálu, který je odeslán do Media Services. Příspěvek kanál může obsahovat signály související s reklamy, jako je například SCTE 35 značky.
- Komponenty ve službě Media Services, která umožňuje ingestovat, ve verzi preview, balení, záznamu, šifrování a vysílat živě přenášená akce vašim zákazníkům nebo do sítě CDN pro další distribuci.

Tento článek poskytuje podrobný přehled najdete pokyny a zahrnuje diagramy hlavní součásti účastnící se živé streamování pomocí služby Media Services.

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Tady jsou kroky pro pracovní postup živého streamování:

1. Vytvoření **živá událost**.
2. Vytvořte nový **Asset** objektu.
3. Vytvoření **Live výstup** a používat název assetu, kterou jste vytvořili.
4. Vytvoření **streamování zásad** a **klíč obsahu** Pokud máte v úmyslu šifrování obsahu pomocí DRM.
5. Pokud nepoužíváte DRM, vytvořte **Lokátor streamování** pomocí integrované **streamování zásad** typy.
6. Seznam cest na **streamování zásad** získat zpět adresy URL používat (Toto jsou deterministické).
7. Získání názvu hostitele pro **koncový bod streamování** chcete z datový proud stream (ujistěte se, že je spuštěný koncový bod streamování). 
8. Adresu URL v kroku 6 v kombinaci s názvem hostitele v kroku 7 zobrazíte úplnou adresu URL.
9. Pokud budete chtít zastavit, aby vaše **živá událost** viditelná, musíte zastavit streamování události tak, že odstraníte **Lokátor streamování**.

Další informace najdete v tématu [živého streamování kurzu](stream-live-tutorial-with-api.md) , který je založen na [Live .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) vzorku.

## <a name="overview-of-main-components"></a>Přehled hlavních komponent

Pokud chcete doručovat na vyžádání a živé datové proudy pomocí služby Media Services, musíte mít aspoň jeden [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Při vytvoření účtu Media Services **výchozí** StreamingEndpoint se přidá k němu **Zastaveno** stavu. Je nutné začít StreamingEndpoint, ze kterého chcete Streamovat obsah do vašeho prohlížeče. Můžete použít výchozí **StreamingEndpoint**, nebo vytvořte další přizpůsobené **StreamingEndpoint** požadované konfigurace a nastavení CDN. Můžete se rozhodnout povolit více koncové body streamování, každý z nich cílení na různé sítě CDN a poskytuje jedinečný název hostitele pro doručování obsahu. 

Ve službě Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za příjem a zpracování živého videa informačních kanálů. Při vytváření Livestream se vytvoří, můžete použít k odesílání živě signál z vzdálený kodér vstupní koncový bod. Vzdáleném kodér služby live Encoding odešle informační kanál k příspěvek vstupní koncový bod buď pomocí [RTMP](https://www.adobe.com/devnet/rtmp.html) nebo [technologie Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protocol (fragmentovaný soubor MP4). Pro technologii Smooth Streaming ingestování, jsou podporovaná schémata URL `http://` nebo `https://`. Protokol ingestování RTMP, jsou podporovaná schémata URL `rtmp://` nebo `rtmps://`. Další informace najdete v tématu [doporučená živého streamování kodérů](recommended-on-premises-live-encoders.md).

Jakmile **Livestream** spustí příjem příspěvků datového kanálu, můžete použít svůj koncový bod ve verzi preview (Náhled adresy URL pro zobrazení náhledu a ověření, že vám posíláme živého datového proudu před dalším publikování. Po kontrole, že datový proud ve verzi preview je dobré, vám pomůže Livestream uvolněte živého datového proudu pro doručení prostřednictvím jednoho nebo více (předem vytvořené) **koncové body streamování**. Chcete-li to provést, vytvořte nový [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) na **Livestream**. 

**LiveOutput** objektu je jako pásku rekordéru, který bude zachytávat a poznamenejte si live stream do prostředku ve vašem účtu Media Services. Zaznamenaný obsah bude trvale uložit do účtu služby Azure Storage, který je připojený ke svému účtu, do kontejneru definované pomocí prostředků resource.  **LiveOuput** také umožňuje řídit některé vlastnosti odchozí živého datového proudu, jako je například kolik datového proudu se ukládají v záznamu archivu (například kapacita cloudového DVR). Archiv na disku je do kruhové archivu "okno", který obsahuje pouze množství obsahu, který je zadán v **archiveWindowLength** vlastnost **LiveOutput**. Obsah, který spadá mimo toto okno se automaticky zruší z kontejneru úložiště a nepůjde obnovit. Můžete vytvořit více LiveOutputs (maximálně až tři) na Livestream se archiv různých délek a nastavení.  

Pomocí služby Media Services můžete využít výhod **dynamické balení**, která umožňuje zobrazit náhled a všesměrového vysílání živé streamy v [formátů MPEG DASH, HLS a Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z příspěvku informačního kanálu že odesíláte do služby. Vaši uživatelé můžou přehrávat živé streamování pomocí libovolné kompatibilní hráči HLS, DASH nebo Smooth Streaming. Můžete použít [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) ve vašich webových nebo mobilních aplikací můžete poskytovat datový proud v některém z těchto protokolů.

Služba Media Services umožňuje doručovat obsah zašifrovaný dynamicky (**dynamického šifrování**) s Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM licence autorizovaným klientům. Další informace o tom, jak šifrování obsahu pomocí služby Media Services najdete v tématu [ochrana obsahu – přehled](content-protection-overview.md)

V případě potřeby můžete také použít dynamické filtrování, který slouží k řízení počet stop, formáty, přenosových rychlostí a prezentace časových oken, které se pošlou hráči. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

### <a name="new-capabilities-for-live-streaming-in-v3"></a>Nové možnosti pro živé streamování ve verzi 3

S v3 rozhraní API z Media Services můžete využívat následující nové funkce:

- Nový režim s nízkou latencí. Další informace najdete v tématu [latence](live-event-latency.md).
- (Zvýšení stability a další podporu zdrojového kodér) Vylepšená podpora RTMP ve službě.
- Ingestování RTMPS zabezpečené.<br/>Při vytváření Livestream získáte 4 ingestované adresy URL. Ingestování 4 adresy URL jsou téměř identické, mít stejný token streamování (AppId), jenom část čísla portu se liší. Dva z adres URL jsou primární a záložní pro RTMPS.   
- Můžete Streamovat živé události, které jsou dlouhé až 24 hodin, pokud informační kanál pomocí služby Media Services pro překódování příspěvek s jednou přenosovou rychlostí do výstupního datového proudu, který má více přenosových rychlostí. 

## <a name="liveevent-types"></a>Typy Livestream

A [Livestream](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: Předávací tak pro živé kódování. 

### <a name="pass-through"></a>Průchod

![Předávací](./media/live-streaming/pass-through.png)

Při použití průchozích Livestream se spoléháte na vaše místní kodér služby live Encoding pro vygenerování více datový proud videa s přenosovou rychlostí a odeslat, že jako příspěvek informační kanál k Livestream (pomocí protokolu RTMP nebo fragmentovaný soubor MP4). Livestream se pak provede prostřednictvím příchozí datové proudy videa bez dalšího zpracování. Předávací Livestream je optimalizovaný pro dlouho běžící živě přenášené události nebo 24 × 365 lineární živé streamování. Při vytváření tohoto typu Livestream, zadejte None (LiveEventEncodingType.None).

Můžete odeslat příspěvek kanálu na rozlišení až 4 kB a v objektu frame míra 60 snímků za sekundu s H.264/AVC nebo H.265/HEVC video kodeků a AAC (AAC-LC, HE-AACv1 nebo HE-AACv2) zvukový kodek.  Zobrazit [Livestream typy porovnání a omezení](live-event-types-comparison.md) , kde najdete další podrobnosti.

> [!NOTE]
> Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
> 

Podívejte se na příklad za provozu v [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kódování v reálném čase  

![živé kódování](./media/live-streaming/live-encoding.png)

Pokud používáte živé kódování pomocí Media Services, nakonfigurujete by vaše místní kodér služby live Encoding odesílat videa s jednou přenosovou rychlostí jako příspěvek informačního kanálu Livestream (pomocí protokolu RTMP nebo fragmentovaný soubor Mp4). Livestream kóduje této příchozí s jednou přenosovou rychlostí na datový proud stream [více přenosovými rychlostmi datový proud videa](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), zpřístupní pro doručení pro přehrávání zařízení prostřednictvím protokolů, jako jsou MPEG-DASH, HLS a Smooth Streaming. Při vytváření tohoto typu Livestream, zadejte jako typ kódování **standardní** (LiveEventEncodingType.Standard).

Můžete odeslat příspěvek kanálu na až rozlišení 1080 p rychlostí rámec 30 snímků/druhé kodek H.264/AVC videa a AAC (AAC-LC, HE-AACv1 nebo HE-AACv2) zvukový kodek. Zobrazit [Livestream typy porovnání a omezení](live-event-types-comparison.md) , kde najdete další podrobnosti.

## <a name="liveevent-types-comparison"></a>Porovnání typů Livestream

V následujícím článku obsahuje tabulku, která obsahuje porovnání funkcí těchto dvou typů Livestream: [Porovnání](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) vám umožňuje řídit vlastnosti odchozí živého datového proudu, například kolik datového proudu je zaznamenán (např. kapacita cloudového DVR) a zda prohlížeče můžete spustit sledování živého datového proudu. Vztah mezi **Livestream** a jeho **LiveOutput**vztah s je podobná tradičním televizní vysílání, kterým kanál (**Livestream**) představuje nepřetržitý datový proud videa a nahrávání (**LiveOutput**) působí na určité časové segmentů (například večer novinky od 18:30:00 do 19:00:00). Můžete zaznamenat televizoru pomocí záznamu pro digitální Video (DVR) – ekvivalentní funkce v LiveEvents se spravuje přes vlastnost ArchiveWindowLength. Je formátu ISO 8601 časový interval doba trvání (například PTHH:MM:SS), která určuje kapacitu DVR a můžete nastavit na minimálně 3 minuty, které maximálně 25 hodin.

> [!NOTE]
> **LiveOutput**s spuštění při vytvoření a zastavení při odstranění. Když odstraníte **LiveOutput**, nejsou odstranění podkladové **Asset** a obsahu v prostředku. 
>
> Pokud jste publikovali **Lokátor streamování**s prostředků pro **LiveOutput**, událost (až do délky okna DVR) bude dál zobrazit až koncový čas **Lokátor streamování**  nebo do při odstranění Lokátor, podle toho, co nastane dřív.   

Další informace najdete v tématu [použití cloudového DVR](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

Jakmile máte datový proud plyne do **Livestream**, streamování událostí můžete začít tak, že vytvoříte **Asset**, **LiveOutput**, a **StreamingLocator** . To bude archivovat datového proudu a zpřístupní ji se divákům prostřednictvím [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Po vytvoření účtu Media Services je ke svému účtu v zastaveném stavu přidá výchozí koncový bod streamování. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete Streamovat obsah musí být ve spuštěném stavu.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />Stavy Livestream a fakturace

Livestream začne fakturace jako jeho stav přejde do **systémem**. Pokud chcete zastavit Livestream z fakturace, je nutné zastavit Livestream.

Podrobné informace najdete v tématu [stavy a fakturace](live-event-states-billing.md).

## <a name="latency"></a>Latence

Podrobné informace o latenci LiveEvents najdete v tématu [latence](live-event-latency.md).

## <a name="next-steps"></a>Další postup

- [Porovnání typů Livestream](live-event-types-comparison.md)
- [Stavy a fakturace](live-event-states-billing.md)
- [Latence](live-event-latency.md)
