---
title: Koncepty živých událostí a živých výstupů ve službě Azure Media Services v3
titleSuffix: Azure Media Services
description: Toto téma obsahuje přehled živých událostí a živých výstupů ve službě Azure Media Services v3.
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
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8a00f7c0ec76510cc521966acf98b7250e723697
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985894"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Živé události a živé výstupy v mediálních službách

Azure Media Services vám umožní doručovat živé události svým zákazníkům v cloudu Azure. Chcete-li nastavit živé vysílání událostí v Media Services v3, musíte pochopit koncepty popsané v tomto článku.

> [!TIP]
> Pro zákazníky migrující z media services v2 API, **live událost** entita nahradí **kanál** v 2 a **live výstup** nahradí **program**.

## <a name="live-events"></a>Živé události

[Živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za ingestování a zpracování informačních kanálů živého videa. Při vytváření živé události je vytvořen primární a sekundární vstupní koncový bod, který můžete použít k odeslání živého signálu ze vzdáleného kodéru. Vzdálený živý kodér odešle informační kanál příspěvku do tohoto vstupního koncového bodu pomocí vstupního protokolu [RTMP](https://www.adobe.com/devnet/rtmp.html) nebo [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmentovaný MP4). Pro protokol singestu RTMP může být obsah`rtmp://`odeslán v jasném (`rtmps://`) nebo bezpečně šifrován na drátě( ). Pro protokol ingestování plynulého streamování `http://` jsou `https://`podporovaná schémata adres URL nebo .  

## <a name="live-event-types"></a>Typy živých událostí

[Živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být nastavena buď na *předávací* (místní živý kodér odešle datový proud s více datovým tokem) nebo *živé kódování* (místní živý kodér odešle jeden datový proud datového toku). Typy jsou nastaveny během vytváření pomocí [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None**: Místní živý kodér odešle datový proud více přenosových rychlostí. Příjem datového proudu prochází live událost bez dalšího zpracování. Nazývá se také průchozí režim.
* **LiveEventEncodingType.Standard**: Místní živý kodér odešle jeden datový proud datového toku do služby Live Event a Media Services vytvoří více datových proudů datového toku. Pokud je zdroj příspěvku rozlišení 720p nebo vyšší, předvolba **Default720p** zakóduje sadu párů rozlišení 6 a přenosových rychlostí.
* **LiveEventEncodingType.Premium1080p**: Místní živý kodér odešle jeden datový proud datového toku do živé události a media services vytvoří více datových proudů datového toku. Přednastavení Default1080p určuje výstupní sadu dvojic rozlišení/přenosových rychlostí.

### <a name="pass-through"></a>Průchod

![předávací živé události s ukázkovým diagramem mediálních služeb](./media/live-streaming/pass-through.svg)

Při použití předávané **živé události** se spoléháte na váš místní kodér pro kódování v reálném čase, že vygeneruje stream videa s několika přenosovými rychlostmi a odešle ho jako informační kanál příspěvku do živé události (pomocí protokolu RTMP nebo fragmentovaného MP4). Živá událost potom přenese příchozí streamy videa bez dalšího zpracování. Taková předávací živá událost je optimalizována pro dlouhotrvající živé události nebo lineární živé vysílání 24x365. Při vytváření tohoto typu živé události zadejte None (LiveEventEncodingType.None).

Můžete odeslat informační kanál příspěvku v rozlišeních do 4K a s obnovovací frekvencí 60 snímků za sekundu, s video kodekem buď H.264/AVC, nebo H.265/HEVC a se zvukovým kodekem AAC (AAC-LC, HE-AACv1 nebo HE-AACv2). Další informace naleznete v tématu [Porovnání typů živých událostí](live-event-types-comparison.md).

> [!NOTE]
> Použití předávací metody je nejekonomičtější způsob, jak provést živé streamování, když děláte více událostí po dlouhou dobu a již jste investovali do místních kodéry. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
>

Viz příklad kódu .NET v [aplikaci MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kódování v reálném čase  

![živé kódování pomocí ukázkového diagramu služby Media Services](./media/live-streaming/live-encoding.svg)

Při použití živého kódování se službou Media Services nakonfigurujete místní živý kodér tak, aby odesílal jedno video s přenosovou rychlostí jako kanál příspěvku do živé události (pomocí protokolu RTMP nebo Fragmented-Mp4). Potom nastavíte živou událost tak, aby kódovala příchozí datový proud datového toku do [datového proudu s více datovými toky](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)a zpřístupňovala výstup pro doručení pro přehrávání zařízení prostřednictvím protokolů, jako jsou MPEG-DASH, HLS a Smooth Streaming.

Při použití živého kódování můžete kanál příspěvků odesílat pouze v rozlišení chodech až 1080p s kmitočetem snímků 30 snímků za sekundu, s kodekem H.264/AVC video a AAC (AAC-LC, HE-AACv1 nebo HE-AACv2) zvukovým kodekem. Všimněte si, že předávací živé události mohou podporovat rozlišení až 4K při 60 snímcích za sekundu. Další informace naleznete v tématu [Porovnání typů živých událostí](live-event-types-comparison.md).

Rozlišení a přenosové rychlosti obsažené ve výstupu z živého kodéru jsou určeny přednastavením. Pokud používáte **standardní** živý kodér (LiveEventEncodingType.Standard), pak přednastavení *Default720p* určuje sadu šesti párů rozlišení/přenosové rychlosti, od 720p při 3,5 Mb/s až po 192p při 200 kbps. V opačném případě, pokud používáte **premium1080p** živé kodér (LiveEventEncodingType.Premium1080p), pak *Default1080p* přednastavení určuje sadu šesti párů rozlišení / přenosovou rychlost, bude od 1080p na 3,5 Mbps dolů na 180p na 200 kbps. Další informace najdete v tématu [Předvolby sytému](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Pokud potřebujete přizpůsobit přednastavení živého kódování, otevřete lístek podpory prostřednictvím portálu Azure. Zadejte požadovanou tabulku rozlišení a přenosových rychlostí. Ověřte, zda existuje pouze jedna vrstva v 720p (pokud požadujete přednastavení pro standardní živý kodér) nebo 1080p (pokud požadujete přednastavení pro živý kodér Premium1080p) a maximálně 6 vrstev.

## <a name="creating-live-events"></a>Vytváření živých událostí

### <a name="options"></a>Možnosti

Při vytváření živé události můžete určit následující možnosti:

* Protokol streamování pro živé události (v současné době jsou podporovány protokoly RTMP a Smooth Streaming).<br/>Možnost protokolu nelze změnit, pokud je spuštěna živá událost nebo přidružené živé výstupy. Pokud požadujete různé protokoly, vytvořte pro každý protokol streamování samostatnou živou událost.  
* Při vytváření události můžete určit její automatické spuštění. <br/>Je-li automatické spuštění nastaveno na hodnotu true, bude živá událost spuštěna po vytvoření. Fakturace se spustí, jakmile se spustí živá událost. Chcete-li zastavit další fakturaci, je nutné explicitně volat Stop na prostředku živé události. Případně můžete spustit událost, když jste připraveni začít streamovat.

    Další informace naleznete v tématu [Stavy živých událostí a fakturace](live-event-states-billing.md).

* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které mohou ingestovat video do této živé události. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nejsou zadány žádné adresy IP a neexistuje žádná definice pravidla, nebude povolena žádná adresa IP. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: IpV4 adresa se čtyřmi čísly nebo rozsah adres CIDR.

    Pokud chcete povolit určité IP adresy na vlastních firewallech nebo chcete omezit vstupy na vaše živé události na IP adresy Azure, stáhněte si soubor JSON z [rozsahů IP adres Datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Podrobnosti o tomto souboru vyberte oddíl **Podrobnosti** na stránce.
    
* Při vytváření události můžete zapnout živé přepisy. <br/> Ve výchozím nastavení je živý přepis zakázán. Tuto vlastnost nelze změnit, pokud jsou spuštěny živé události nebo přidružené živé výstupy. 

### <a name="naming-rules"></a>Pravidla pojmenování

* Maximální název živé události je 32 znaků.
* Název by měl následovat tento `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`vzor [regulárního výrazu:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

Viz také [konvence pojmenování koncových bodů streamování](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Chcete-li zaručit jedinečnost názvu živé události, můžete vygenerovat identifikátor GUID a poté odebrat všechny spojovníky a složené závorky (pokud existuje). Řetězec bude jedinečný ve všech živých událostech a jeho délka je zaručena 32.

## <a name="live-event-ingest-urls"></a>Adresy URL s ingestování živých událostí

Po vytvoření živé události můžete získat ingestované adresy URL, které poskytnete živému místnímu kodéru. Kodér pro kódování v reálném čase tyto adresy URL používá ke vkládání živého proudu. Další informace naleznete [v tématu Doporučené místní živé kodéry](recommended-on-premises-live-encoders.md).

Můžete použít buď nejednoduché adresy URL, nebo jednoduché adresy URL.

> [!NOTE] 
> Chcete-li, aby byla adresa URL ingestování prediktivní, nastavte režim "marnost".

* Adresa URL, která není marností

    Adresa URL, která není ješitná, je výchozí režim ve službě Media Services v3. Živou událost získáte potenciálně rychle, ale adresa URL ingestu je známá až při spuštění živé události. Pokud živou událost zastavíte nebo spustíte, adresa URL se změní. <br/>Non-Vanity je užitečné ve scénářích, kdy koncový uživatel chce streamovat pomocí aplikace, kde aplikace chce získat živou událost ASAP a mají dynamickou ingestidovat URL není problém.

    Pokud klientská aplikace nemusí před vytvořením živé události předem vygenerovat adresu URL ingestování, nechte mediální služby automaticky vygenerovat přístupový token pro živou událost.

* Adresa URL marnosti

    Režim vanity upřednostňují velcí provozovatelé vysílání médií, kteří používají hardwarové kodéry vysílání a nechtějí při spuštění živé události překonfigurovat své kodéry. Chtějí prediktivní ingestiční adresu URL, která se v průběhu času nemění.
    
    > [!NOTE]
    > Na webu Azure Portal se adresa URL marnosti nazývá *"Trvalá vstupní adresa URL".*

    Chcete-li zadat tento režim `vanityUrl` `true` v rozhraní API, `false`nastavte na v době vytvoření (výchozí je ). Musíte také předat vlastní přístupový`LiveEventInput.accessToken`token ( ) v době vytvoření. Zadáte hodnotu tokenu, abyste se vyhnuli náhodnému tokenu v adrese URL. Přístupový token musí být platný řetězec GUID (s spojovníky nebo bez něj). Jakmile je režim nastaven, nelze jej aktualizovat.

    Přístupový token musí být ve vašem datovém centru jedinečný. Pokud vaše aplikace potřebuje použít adresu URL marnosti, doporučujeme vždy vytvořit novou instanci IDENTIFIKÁTOR GUID pro přístupový token (namísto opakovaného použití existujícího identifikátoru GUID).

    Pomocí následujících rozhraní API povolte adresu URL vanity a nastavte přístupový `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`token na platný identifikátor GUID (například).  

    |Jazyk|Povolit adresu URL marnosti|Nastavení přístupového tokenu|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |Rozhraní příkazového řádku|[--marnost-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--přístupový token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[Token LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Pravidla pojmenování adresy URL živého ingestu

* Řetězec *random* dále je 128bitové šestnáctkové číslo (skládající se z 32 znaků 0-9 a-f).
* *váš přístupový token*: Platný řetězec GUID, který nastavíte při použití režimu marnosti. Například, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *Název datového proudu*: Označuje název datového proudu pro konkrétní připojení. Hodnota názvu datového proudu je obvykle přidána živým kodérem, který používáte. Živý kodér můžete nakonfigurovat tak, aby k popisu připojení popisoval libovolný název, například "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>Adresa URL, která není marností

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Adresa URL marnosti

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adresa URL náhledu živé události

Jakmile živá událost začne přijímat informační kanál příspěvků, můžete použít jeho koncový bod náhledu k zobrazení náhledu a ověření, že dostáváte živý datový proud před dalším publikováním. Po kontrole, že stream náhledu je dobrý, můžete použít živý přenos k zpřístupnění živého datového proudu pro doručení prostřednictvím jednoho nebo více (předem vytvořených) koncových bodů streamování. Chcete-li to provést, vytvořte nový [živý výstup](https://docs.microsoft.com/rest/api/media/liveoutputs) na živé události.

> [!IMPORTANT]
> Ujistěte se, že video teče do náhledu URL před pokračováním!

## <a name="live-event-long-running-operations"></a>Dlouhotrvající operace živé události

Podrobnosti naleznete v [tématu dlouhotrvající operace](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Živé výstupy

Jakmile budete mít datový proud proudící do živé události, můžete začít streamovací událost vytvořením [assetu](https://docs.microsoft.com/rest/api/media/assets), [živého výstupu](https://docs.microsoft.com/rest/api/media/liveoutputs)a [lokátoru streamování](https://docs.microsoft.com/rest/api/media/streaminglocators). Živý výstup bude archivovat datový proud a zpřístupnit jej divákům prostřednictvím [koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Podrobné informace o živých výstupech naleznete [v tématu Použití cloudového dvr](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Podívejte se na článek [Nejčastější dotazy.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Ptejte se a získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

[Živé streamování tutorial](stream-live-tutorial-with-api.md)
