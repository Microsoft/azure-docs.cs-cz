---
title: Koncepce živých událostí a živých výstupů v Azure Media Services V3
titleSuffix: Azure Media Services
description: Toto téma poskytuje přehled živých událostí a živých výstupů v Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 300d9e433b4c57f2868416d866f1dcff6c189fb7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784452"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Živé události a živé výstupy v Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services vám umožní doručovat živé události zákazníkům v cloudu Azure. Chcete-li nastavit události živého streamování v Media Services V3, je nutné porozumět konceptům, které jsou popsány v tomto článku.

> [!TIP]
> Pro zákazníky, kteří migrují z Media Services V2 rozhraní API, nahradí entita **živá událost** **kanál** ve verzi v2 a **program** pro **živý výstup** nahradí.

## <a name="live-events"></a>Živě streamované události

[Živé události](/rest/api/media/liveevents) jsou zodpovědné za ingestování a zpracování aktivních informačních kanálů. Když vytvoříte živou událost, vytvoří se primární a sekundární vstupní koncový bod, který můžete použít k odeslání živého signálu ze vzdáleného kodéru. Vzdálený živý kodér pošle do tohoto vstupního koncového bodu kanál příspěvků pomocí vstupního protokolu [RTMP](https://www.adobe.com/devnet/rtmp.html) nebo [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (fragmentovaný-MP4). Pro protokol ingestování RTMP se dá obsah poslat jasným ( `rtmp://` ) nebo bezpečně šifrovaným na lince ( `rtmps://` ). Pro protokol Smooth Streaming ingestování jsou podporovaná schémata URL `http://` nebo `https://` .  

## <a name="live-event-types"></a>Typy živých událostí

[Živá událost](/rest/api/media/liveevents) může být nastavená na *předávací* (místní živý kodér posílá datový proud s více přenosovými rychlostmi) nebo *živé kódování* (místní kodér Live Encoder posílá datový proud s jednou přenosovou rychlostí). Typy jsou nastaveny během vytváření pomocí [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** : místní kodér Live posílá datový proud s více přenosovými rychlostmi. Příjmový Stream projde přes živou událost bez dalšího zpracování. Také se nazývá předávací režim.
* **LiveEventEncodingType. Standard** : místní živý kodér odesílá datový proud s jednou přenosovou rychlostí do živé události a Media Services vytvoří více datových proudů s přenosovou rychlostí. Pokud je informační kanál příspěvku z řešení 720p nebo vyšší, **Default720p** předvolba zakóduje sadu párů s 6 páry rozlišení/přenosů.
* **LiveEventEncodingType. Premium1080p** : místní živý kodér odesílá datový proud s jednou přenosovou rychlostí do živé události a Media Services vytvoří více datových proudů s přenosovou rychlostí. Předvolba Default1080p určuje výstupní sadu párů rozlišení/přenosové rychlosti.

### <a name="pass-through"></a>Průchod

![předávací živá událost s Media Services ukázkovým diagramem](./media/live-streaming/pass-through.svg)

Při použití předávací **živé události** se spoléháte na váš místní živý kodér a vygenerujete datový proud s více přenosovými rychlostmi a odešlete ho jako kanál příspěvků do živé události (pomocí RTMP nebo fragmentu protokolu-MP4). Živá událost pak provede přes příchozí datové proudy videa bez dalšího zpracování. Taková předávací živá událost je optimalizovaná pro dlouhotrvající živé události nebo 24x365 lineární živé streamování. Při vytváření tohoto typu živé události zadejte None (LiveEventEncodingType. None).

Můžete odeslat informační kanál příspěvku v rozlišeních do 4K a s obnovovací frekvencí 60 snímků za sekundu, s video kodekem buď H.264/AVC, nebo H.265/HEVC a se zvukovým kodekem AAC (AAC-LC, HE-AACv1 nebo HE-AACv2). Další informace naleznete v tématu [porovnání typů událostí typu Live](live-event-types-comparison.md).

> [!NOTE]
> Použití předávací metody je nejúčinnější způsob, jak provádět živé streamování, když provádíte více událostí po dlouhou dobu a už jste investovali do místních kodérů. Podívejte se na podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/) .
>

Podívejte se na příklad kódu .NET v [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kódování v reálném čase  

![živé kódování pomocí Media Services ukázkový diagram](./media/live-streaming/live-encoding.svg)

Pokud používáte živé kódování s Media Services, nakonfigurujete místní kodér Live Encoder k odeslání videa s jednou přenosovou rychlostí jako kanálu příspěvků do živé události (pomocí RTMP nebo Fragmented-Mp4ho protokolu). Pak nastavíte živou událost tak, aby se zakódujíy příchozí datový proud s jednou přenosovou rychlostí do [více datových streamů s více přenosovými rychlostmi](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), a výstup bude dostupný pro doručování do zařízení přes protokoly, jako je MPEG-pomlčka, HLS a Smooth Streaming.

Při použití živého kódování můžete odeslat kanál příspěvku pouze v rozlišení až do rozlišení 1080p za snímkovou rychlostí 30 snímků za sekundu a s použitím kodeku H. 264/AVC a zvukového kodeku AAC (AAC-LC, HE-AACv1 nebo AACv2). Upozorňujeme, že průchozí živé události můžou podporovat řešení až 4K na 60 snímků za sekundu. Další informace naleznete v tématu [porovnání typů událostí typu Live](live-event-types-comparison.md).

Rozlišení a přenosové rychlosti obsažené ve výstupu z kodéru Live Encoder se určují pomocí přednastavení. Pokud používáte **standardní** Live Encoder (LiveEventEncodingType. Standard), pak předvolba *Default720p* určuje sadu šesti párů rozlišení a přenosové rychlosti z 720P v 3,5 MB/s až do 192p při 200 KB/s. V opačném případě, pokud používáte **Premium1080p** Live Encoder (LiveEventEncodingType. Premium1080p), pak přednastavení *Default1080p* určuje sadu šesti párů rozlišení a přenosové rychlosti z 1080p po 3,5 MB/s až do 180p na 200 KB/s. Další informace najdete v tématu [Předvolby sytému](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování, otevřete lístek podpory prostřednictvím Azure Portal. Určete požadovanou tabulku s rozlišením a přenosovými rychlostmi. Ověřte, zda je v 720p pouze jedna vrstva (Pokud požadujete předvolbu pro standardní Live Encoder) nebo v 1080p (Pokud požadujete přednastavení pro Premium1080p Live Encoder) a 6 vrstev nejvíce.

## <a name="creating-live-events"></a>Vytváření živých událostí

### <a name="options"></a>Možnosti

Při vytváření živé události můžete zadat následující možnosti:

* Živé události můžete zadat s názvem a popisem.
* Cloudové kódování zahrnuje předávací (bez kódování v cloudu), Standard (až 720p) nebo Premium (až do 1080p). Pro kódování standard a Premium můžete zvolit režim roztažení kódovaného videa.
  * Žádné: striktně respektuje rozlišení výstupu zadané v přednastaveném kódování bez zvážení poměru stran pixelů nebo poměru stran pro vstupní video.
  * AutoSize: přepíše výstupní rozlišení a změní ho tak, aby odpovídal poměru stran zobrazení vstupu, bez odsazení. Například pokud je vstup 1080 a předvolby kódování požádá o 1280x1280, pak je hodnota v přednastavence přepsána a výstup bude na 1280 × 720, který bude udržovat poměr stran vstupních hodnot 16:9.
  * Přizpůsobit: vyplní výstup (s polem Letterbox nebo pilíře), aby se vyhodnotilo výstupní řešení, a současně zajistí, že aktivní oblast videa ve výstupu má stejný poměr stran jako vstup. Například pokud je vstup 1080 a předvolby kódování požádá o 1280x1280, pak výstup bude v 1280x1280, který obsahuje vnitřní obdélník 1280 × 720 v poměru stran 16:9, s oblastmi pole pilíře 280 pixelů na levé a pravé straně.
* Protokol streamování (v současné době se podporují i protokoly RTMP a Smooth Streaming). Možnost protokolu se nedá změnit, když je spuštěná živá událost nebo její přidružený živý výstup. Pokud požadujete různé protokoly, vytvořte samostatnou živou událost pro každý protokol pro streamování.
* ID vstupu, které je globálně jedinečný identifikátor pro vstupní datový proud živé události.
* Předpona statického názvu hostitele, která zahrnuje žádné (v takovém případě se použije náhodný řetězec s šestnáctkovou 128), použijte název živé události nebo použijte vlastní název.  Pokud se rozhodnete použít název zákazníka, tato hodnota je vlastní Předpona názvu hostitele.
* Můžete snížit koncovou latenci mezi živým vysíláním a přehráváním nastavením intervalu vstupních snímků, který je (v sekundách) pro každý segment média ve výstupu HLS. Hodnota by měla být nenulové celé číslo v rozsahu od 0,5 do 20 sekund.  Pokud nejsou nastavené *ani žádné* intervaly snímků vstupních nebo výstupních klíčů, hodnota se ve výchozím nastavení použije na 2 sekundy. Interval klíčových snímků je povolený jenom u předávacích událostí.
* Při vytváření události je možné ji nastavit na automatické spuštění. Pokud je vlastnost autostart nastavena na hodnotu true, spustí se po vytvoření živá událost. Fakturace začne ihned po spuštění živé události. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události. Alternativně můžete událost spustit až po dokončení spuštění streamování.

> [!NOTE]
> Maximální Snímková frekvence je 30 snímků pro kódování standard a Premium.

## <a name="standby-mode"></a>Pohotovostní režim

Když vytvoříte živou událost, můžete ji nastavit na úsporný režim. I když je událost v pohotovostním režimu, můžete upravit popis, předponu statického názvu hostitele a omezit nastavení přístupu Input a Preview.  Pohotovostní režim je stále fakturovatelný, ale je cenově odlišný než při spuštění živého datového proudu.

Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing.md).

* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které můžou ingestovat video do této živé události. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).
<br/><br/>
Pokud nejsou zadané žádné IP adresy a není k dispozici žádná definice pravidla, nebude povolena žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: IpV4 adresa se čtyřmi čísly nebo rozsahem adres CIDR.
<br/><br/>
Pokud chcete povolit určité IP adresy pro vlastní brány firewall nebo chcete omezit vstupy na živé události na IP adresy Azure, Stáhněte si soubor JSON z [rozsahů IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Podrobnosti o tomto souboru zobrazíte výběrem části **Podrobnosti** na stránce.

* Při vytváření události se můžete rozhodnout zapnout funkci Live přepisů. Ve výchozím nastavení je živý přepis zakázán. Další informace o živém přepisu najdete v tématu [živý přepis](live-transcription.md).

### <a name="naming-rules"></a>Pravidla pojmenování

* Maximální název živé události je 32 znaků.
* Název by měl splňovat tento vzor [regulárního výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Viz také [zásady vytváření názvů koncových bodů streamování](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Chcete-li zaručit jedinečnost názvu živé události, můžete vygenerovat identifikátor GUID a pak odebrat všechny spojovníky a složené závorky (pokud existují). Řetězec bude jedinečný napříč všemi živými událostmi a jeho délka bude zaručena 32.

## <a name="live-event-ingest-urls"></a>Adresy URL pro příjem živých událostí

Jakmile se vytvoří živá událost, můžete získat adresy URL pro ingestování, které poskytnete pro živý místní kodér. Kodér pro kódování v reálném čase tyto adresy URL používá ke vkládání živého proudu. Další informace najdete v tématu [Doporučené místní kodéry v reálném čase](recommended-on-premises-live-encoders.md).

>[!NOTE]
> Od verze rozhraní 2020-05-01 API jsou adresy URL individuální známé jako názvy statických hostitelů.

Můžete použít buď nejednoduché adresy URL, nebo jednoduché adresy URL.

> [!NOTE]
> Aby se adresa URL pro ingestování mohla odhadnout, nastavte režim "individuální".

* Jiná než individuální adresa URL

    Jiná než individuální adresa URL je výchozím režimem v Media Services V3. Živá událost může být rychle rychlá, ale příjem adresy URL je známý jenom v případě, že je spuštěná živá událost. Adresa URL se změní, pokud zastavíte nebo spustíte živou událost. Non-individuální je užitečná ve scénářích, kdy chce koncový uživatel streamovat pomocí aplikace, ve které aplikace chce získat živou událost a že Dynamická adresa URL příjmu není problém.

    Pokud klientská aplikace nemusí před vytvořením živé události předem generovat adresu URL pro příjem dat, nechte Media Services automaticky vygenerovat přístupový token pro živou událost.

* Adresa URL individuální

    Individuální režim upřednostňuje velké všesměrové vysílání médií, kteří používají kodéry všesměrového vysílání hardwaru a nechtějí překonfigurovat kodéry, když spustí živou událost. Tyto všesměrové vysílání chtějí prediktivní příjem adres URL, který se v průběhu času nemění.

    > [!NOTE]
    > V Azure Portal se adresa URL individuální nazývá " *předpona statického názvu hostitele* ".

    Chcete-li zadat tento režim v rozhraní API, nastavte `useStaticHostName` na hodnotu `true` při vytváření (výchozí nastavení je `false` ). Když `useStaticHostname` je nastavená hodnota true, `hostnamePrefix` Určuje první část názvu hostitele přiřazeného k živým koncovým bodům a koncovým bodům ingestování událostí. Poslední název hostitele by byl kombinací této předpony, názvu účtu mediální služby a krátkého kódu pro Azure Media Services datové centrum.

    Chcete-li se vyhnout náhodnému tokenu v adrese URL, je také nutné před vytvořením předat vlastní přístupový token ( `LiveEventInput.accessToken` ).  Přístupový token musí být platný řetězec GUID (s pomlčkami nebo bez nich). Po nastavení je režim nepůjde aktualizovat.

    Přístupový token musí být ve vašem datovém centru jedinečný. Pokud vaše aplikace potřebuje použít adresu URL individuální, doporučujeme vždy vytvořit novou instanci GUID pro přístupový token (místo opětovného použití existujícího identifikátoru GUID).

    Pomocí následujících rozhraní API povolte adresu URL individuální a nastavte přístupový token na platný identifikátor GUID (například `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Jazyk|Povolit individuální adresu URL|Nastavení přístupového tokenu|
    |---|---|---|
    |REST|[Properties. vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--individuální-URL](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--Access-token](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[Livestream. VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Pravidla pro pojmenování adres URL pro živá přijímání

* Řetězec *random* dále je 128bitové šestnáctkové číslo (skládající se z 32 znaků 0-9 a-f).
* *váš přístupový token* : platný řetězec GUID, který jste nastavili při použití režimu individuální. Například, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *název streamu* : označuje název streamu pro konkrétní připojení. Hodnota názvu datového proudu je obvykle přidána živým kodérem, který používáte. Živý kodér můžete nakonfigurovat tak, aby k popisu připojení používal libovolný název, například: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Jiná než individuální adresa URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Hladké streamování

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Adresa URL individuální

V následujících cestách `<live-event-name>` znamená buď název přidělený události, nebo vlastní název použitý při vytváření živé události.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Hladké streamování

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adresa URL náhledu živé události

Jakmile živá událost začne přijímat kanál příspěvků, můžete použít jeho koncový bod Preview k zobrazení náhledu a ověření, že budete dostávat živý datový proud před dalším publikováním. Po zkontrolování, zda je datový proud verze Preview dobrý, můžete použít živou událost k zajištění, aby živý datový proud byl k dispozici pro doručení prostřednictvím jednoho nebo více (předem vytvořených) koncových bodů streamování. K tomu je potřeba vytvořit nový [živý výstup](/rest/api/media/liveoutputs) na živé události.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že video přetéká do adresy URL náhledu.

## <a name="live-event-long-running-operations"></a>Dlouhodobě běžící operace živé události

Podrobnosti najdete v tématu [dlouhotrvající operace](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Živé výstupy

Jakmile datový proud přetéká do živé události, můžete zahájit streamování událostí vytvořením [assetu](/rest/api/media/assets), [živého výstupu](/rest/api/media/liveoutputs)a [lokátoru streamování](/rest/api/media/streaminglocators). živý výstup bude archivovat datový proud a zpřístupní ho návštěvníkům prostřednictvím [koncového bodu streamování](/rest/api/media/streamingendpoints).  

Podrobné informace o živých výstupech najdete v tématu [Použití cloudového DVR](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Přečtěte si článek [Nejčastější dotazy](frequently-asked-questions.md#live-streaming) .

## <a name="ask-questions-and-get-updates"></a>Klást otázky a získávat aktualizace

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Kurz živého streamování](stream-live-tutorial-with-api.md)
