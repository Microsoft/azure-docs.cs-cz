---
title: Koncepty živého streamování v Azure Media Services živé události a živé výstupy | Microsoft Docs
description: Tento článek obsahuje přehled konceptů živého streamování v Azure Media Services V3.
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
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: efe0aaf7c7d5516401f8c72721121a5dff247b95
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350370"
---
# <a name="live-events-and-live-outputs"></a>Živé události a výstupy

Azure Media Services vám umožní doručovat živé události zákazníkům v cloudu Azure. Pokud chcete konfigurovat události živého streamování v Media Services V3, potřebujete pochopit koncepty popsané v tomto článku.

> [!TIP]
> Pro zákazníky, kteří migrují z Media Services V2 rozhraní API, nahradí entita **živá událost** **kanál** ve verzi v2 a **program**pro **živý výstup** nahradí.

## <a name="live-events"></a>Živé události

[Živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za ingestování a zpracování informačních kanálů živého videa. Když vytvoříte živou událost, vytvoří se primární a sekundární vstupní koncový bod, který můžete použít k odeslání živého signálu ze vzdáleného kodéru. Vzdálený živý kodér pošle do tohoto vstupního koncového bodu kanál příspěvků pomocí vstupního protokolu [RTMP](https://www.adobe.com/devnet/rtmp.html) nebo [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmentovaný-MP4). Pro protokol ingestování RTMP se dá obsah poslat jasným (`rtmp://`) nebo bezpečně šifrovaným na lince (`rtmps://`). Pro protokol Smooth Streaming ingestování jsou `http://` podporovaná schémata URL nebo. `https://`  

## <a name="live-event-types"></a>Typy živých událostí

[Živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: průchozí a živé kódování. Typy jsou nastaveny během vytváření pomocí [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** – místní kodér Live posílá datový proud s více přenosovými rychlostmi. Příjmový Stream projde přes živou událost bez dalšího zpracování. 
* **LiveEventEncodingType. Standard** – místní Live Encoder odesílá datový proud s jednou přenosovou rychlostí do živé události a Media Services vytvoří více datových proudů s přenosovou rychlostí. Pokud je informační kanál příspěvku z řešení 720p nebo vyšší, **Default720p** předvolba zakóduje sadu párů s 6 páry rozlišení/přenosů.
* **LiveEventEncodingType. Premium1080p** – místní Live Encoder odesílá datový proud s jednou přenosovou rychlostí do živé události a Media Services vytvoří více datových proudů s přenosovou rychlostí. Předvolba Default1080p určuje výstupní sadu párů rozlišení/přenosové rychlosti. 

### <a name="pass-through"></a>Průchod

![Průchozí](./media/live-streaming/pass-through.svg)

Při použití předávané **živé události** se spoléháte na váš místní kodér pro kódování v reálném čase, že vygeneruje stream videa s několika přenosovými rychlostmi a odešle ho jako informační kanál příspěvku do živé události (pomocí protokolu RTMP nebo fragmentovaného MP4). Živá událost potom přenese příchozí streamy videa bez dalšího zpracování. Taková předávací živá událost je optimalizovaná pro dlouhotrvající živé události nebo 24x365 lineární živé streamování. Při vytváření tohoto typu živé události zadejte None (LiveEventEncodingType.None).

Můžete odeslat informační kanál příspěvku v rozlišeních do 4K a s obnovovací frekvencí 60 snímků za sekundu, s video kodekem buď H.264/AVC, nebo H.265/HEVC a se zvukovým kodekem AAC (AAC-LC, HE-AACv1 nebo HE-AACv2).  Další podrobnosti najdete v článku popisujícím [porovnání typů živých událostí](live-event-types-comparison.md).

> [!NOTE]
> Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
> 

Podívejte se na příklad kódu .NET v [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kódování v reálném čase  

![Kódování v reálném čase](./media/live-streaming/live-encoding.svg)

Při použití kódování v reálném čase pomocí Media Services nakonfigurujte místní kodér pro kódování v reálném čase tak, aby jako informační kanál příspěvku do živé události odesílal video s jednou přenosovou rychlostí (pomocí protokolu RTMP nebo fragmentovaného MP4). Pak jste nastavili živou událost tak, aby zaznamenala příchozí datový proud s jednou přenosovou rychlostí na [více datových proudů](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), a zpřístupní výstup pro doručování do zařízení přes protokoly, jako jsou MPEG-pomlčka, HLS a Smooth Streaming.

Při použití živého kódování můžete odeslat kanál příspěvku pouze v rozlišení až do rozlišení 1080p za snímkovou rychlostí 30 snímků za sekundu a s použitím kodeku H. 264/AVC a zvukového kodeku AAC (AAC-LC, HE-AACv1 nebo AACv2). Upozorňujeme, že průchozí živé události můžou podporovat řešení až 4K na 60 snímků za sekundu. Další podrobnosti najdete v článku popisujícím [porovnání typů živých událostí](live-event-types-comparison.md).

Rozlišení a přenosové rychlosti obsažené ve výstupu z kodéru Live Encoder se určují pomocí přednastavení. Pokud používáte **standardní** Live Encoder (LiveEventEncodingType. Standard), pak předvolba *Default720p* určí sadu párů s 6 páry rozlišení a přenosové rychlosti z 720P ve 3,5 MB/s až do 192p v 200 KB/s. V opačném případě, pokud používáte **Premium1080p** Live Encoder (LiveEventEncodingType. Premium1080p), pak předvolba *Default1080p* určuje sadu dvojic s 6 MB/bitovou rychlostí z 1080p po 3,5 Mbps až do 180p v 200 KB/s. Další informace najdete v tématu [Předvolby sytému](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování, otevřete prosím lístek podpory prostřednictvím Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, zda je v 720p pouze jedna vrstva (Pokud požadujete předvolbu pro standardní Live Encoder) nebo v 1080p (Pokud požadujete přednastavení pro Premium1080p Live Encoder) a maximálně 6 vrstev.

## <a name="creating-live-events"></a>Vytváření živých událostí 

### <a name="options"></a>Možnosti

Při vytváření živé události můžete zadat následující možnosti:

* Protokol streamování pro živou událost (aktuálně se podporují i protokoly RTMP a Smooth Streaming).<br/>Možnost protokolu nejde změnit, pokud je spuštěná živá událost nebo jejich přidružené živé výstupy. Pokud požadujete různé protokoly, měli byste pro každý protokol streamování vytvořit samostatnou živou událost.  
* Při vytváření události můžete nastavit automatické spouštění. <br/>Pokud je vlastnost autostart nastavena na hodnotu true, spustí se po vytvoření živá událost. Fakturace začne ihned po spuštění živé události. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události. Alternativně můžete událost spustit, až budete připraveni ke spuštění streamování. 

    Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing.md).
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které můžou ingestovat video do této živé události. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nezadáte žádné IP adresy a neexistuje definice pravidla, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: Adresa IpV4 se čtyřmi čísly, rozsahem adres CIDR.

    Pokud chcete povolit určité IP adresy pro vlastní brány firewall nebo chcete omezit vstupy na živé události na IP adresy Azure, Stáhněte si soubor JSON z [rozsahů IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Podrobnosti o tomto souboru získáte kliknutím na část **Podrobnosti** na stránce.
        
### <a name="naming-rules"></a>Pravidla pojmenování

* Maximální název živé události je 32 znaků.
* Název by měl postupovat podle tohoto vzoru [regulárního výrazu](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

Podívejte se také na [zásady vytváření názvů koncových bodů streamování](streaming-endpoint-concept.md#naming-convention).

## <a name="live-event-ingest-urls"></a>Adresy URL pro příjem živých událostí

Po vytvoření živé události můžete získat adresy URL ingestu, které poskytnete místní kodéru pro kódování v reálném čase. Kodér pro kódování v reálném čase tyto adresy URL používá ke vkládání živého proudu. Další informace najdete v tématu [Doporučené místní kodéry v reálném čase](recommended-on-premises-live-encoders.md). 

Můžete použít buď nejednoduché adresy URL, nebo jednoduché adresy URL. 

> [!NOTE] 
> Aby se adresa URL pro ingestování mohla odhadnout, nastavte režim "individuální".

* Jiná než individuální adresa URL

    Jiná než individuální adresa URL je výchozím režimem v Media Services V3. Živou událost získáte potenciálně rychle, ale adresa URL ingestu je známá až při spuštění živé události. Pokud živou událost zastavíte nebo spustíte, adresa URL se změní. <br/>Nejednoduché adresy jsou užitečné v situacích, kdy koncový uživatel chce streamovat pomocí aplikace, která chce získat živou událost co nejdříve, a použití dynamické adresy URL ingestu nepředstavuje žádný problém.
    
    Pokud klientská aplikace nemusí před vytvořením živé události předem generovat adresu URL pro příjem dat, stačí, když Media Services automaticky vygenerovat přístupový token pro živou událost.
* Adresa URL individuální

    Režim jednoduchých adres preferují velké mediální vysílače, které používají hardwarové kodéry vysílání a nechtějí znovu konfigurovat své kodéry při spuštění živé události. Chtějí předvídatelnou adresu URL ingestu, která se během doby nemění.
    
    Chcete-li zadat tento režim, `vanityUrl` nastavte `true` na hodnotu při vytváření (výchozí `false`nastavení je). V době vytváření je také potřeba předat vlastní přístupový token`LiveEventInput.accessToken`(). Zadejte hodnotu tokenu, aby se zabránilo náhodnému tokenu v adrese URL. Přístupový token musí být platný řetězec GUID (s pomlčkami nebo bez nich). Až se režim nastaví, nedá se aktualizovat.

    Přístupový token musí být ve vašem datovém centru jedinečný. Pokud vaše aplikace potřebuje použít adresu URL individuální, doporučujeme vždy vytvořit novou instanci GUID pro přístupový token (místo opětovného použití existujícího identifikátoru GUID). 

    Pomocí následujících rozhraní API povolte adresu URL individuální a nastavte přístupový token na platný identifikátor GUID (například `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Jazyk|Povolit individuální adresu URL|Nastavení přístupového tokenu|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |Rozhraní příkazového řádku|[--individuální-URL](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[Livestream. VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Pravidla pro pojmenování adres URL pro živá přijímání

* Řetězec *random* dále je 128bitové šestnáctkové číslo (skládající se z 32 znaků 0-9 a-f).
* *váš přístupový token* – platný řetězec GUID, který jste nastavili při použití režimu individuální. Například, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *název streamu* – označuje název datového proudu pro konkrétní připojení. Hodnota názvu datového proudu je obvykle přidána živým kodérem, který používáte. Živý kodér můžete nakonfigurovat tak, aby pro popis připojení používal libovolný název, například: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Jiná než individuální adresa URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Adresa URL individuální

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adresa URL náhledu živé události

Jakmile živá událost začne přijímat kanál příspěvků, můžete použít jeho koncový bod Preview k zobrazení náhledu a ověření, že příjem živého datového proudu před dalším publikováním. Po zkontrolování, zda je datový proud verze Preview dobrý, můžete použít živou událost k zajištění dostupnosti živého datového proudu prostřednictvím jednoho nebo více (předem vytvořených) koncových bodů streamování. K tomu je třeba vytvořit nový [živý výstup](https://docs.microsoft.com/rest/api/media/liveoutputs) na živé události. 

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že video přetéká do adresy URL náhledu.

## <a name="live-event-long-running-operations"></a>Dlouhodobě běžící operace živé události

Podrobnosti najdete v tématu [dlouhotrvající operace](media-services-apis-overview.md#long-running-operations) .

## <a name="live-outputs"></a>Živé výstupy

Jakmile datový proud přetéká do živé události, můžete zahájit streamování událostí vytvořením Assetu, živého [](https://docs.microsoft.com/rest/api/media/assets) [výstupu](https://docs.microsoft.com/rest/api/media/liveoutputs)a lokátoru streamování [](https://docs.microsoft.com/rest/api/media/streaminglocators). Živý výstup bude archivovat datový proud a zpřístupní ho návštěvníkům prostřednictvím [koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Podrobné informace o živých výstupech najdete v tématu [Použití cloudového DVR](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Kurz živého streamování](stream-live-tutorial-with-api.md)
