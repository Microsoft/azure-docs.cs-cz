---
title: Živé streamování koncepty ve službě Azure Media Services – živé události a Live výstupy | Dokumentace Microsoftu
description: Tento článek obsahuje přehled živého streamování konceptů v Azure Media Services v3.
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
ms.date: 06/12/2019
ms.author: juliako
ms.openlocfilehash: 49ab52f031e24ac77a534c86061fe831bbec39ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114672"
---
# <a name="live-events-and-live-outputs"></a>Živé události a výstupy

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Pokud chcete nakonfigurovat živě streamovaných událostí v Media Services v3, budete muset pochopit principy probírané v tomto článku.

> [!TIP]
> Pro zákazníky, kteří migrace z rozhraní API služby Media Services v2 **živá událost** nahradí entitu **kanál** ve v2 a **Live výstup** nahradí **Program**.


## <a name="live-events"></a>Živé události

[Živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za ingestování a zpracování informačních kanálů živého videa. Při vytváření živé události se vytvoří vstupní koncový bod, který můžete použít k odesílání živého signálu ze vzdáleného kodéru. Vzdálený kodér pro kódování v reálném čase odešle informační kanál příspěvku do tohoto vstupního koncového bodu buď pomocí [RTMP](https://www.adobe.com/devnet/rtmp.html), nebo pomocí protokolu [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmentovaný MP4). Pro technologii Smooth Streaming ingestování, jsou podporovaná schémata URL `http://` nebo `https://`. Protokol ingestování RTMP, jsou podporovaná schémata URL `rtmp://` nebo `rtmps://`. 

## <a name="live-event-types"></a>Live typy událostí

A [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: Předávací tak pro živé kódování. 

### <a name="pass-through"></a>Průchod

![Průchozí](./media/live-streaming/pass-through.svg)

Při použití předávané **živé události** se spoléháte na váš místní kodér pro kódování v reálném čase, že vygeneruje stream videa s několika přenosovými rychlostmi a odešle ho jako informační kanál příspěvku do živé události (pomocí protokolu RTMP nebo fragmentovaného MP4). Živá událost potom přenese příchozí streamy videa bez dalšího zpracování. Taková předávací živá událost je optimalizovaná pro dlouho běžící živé události nebo lineární živé streamování 24×365. Při vytváření tohoto typu živé události zadejte None (LiveEventEncodingType.None).

Můžete odeslat informační kanál příspěvku v rozlišeních do 4K a s obnovovací frekvencí 60 snímků za sekundu, s video kodekem buď H.264/AVC, nebo H.265/HEVC a se zvukovým kodekem AAC (AAC-LC, HE-AACv1 nebo HE-AACv2).  Další podrobnosti najdete v článku popisujícím [porovnání typů živých událostí](live-event-types-comparison.md).

> [!NOTE]
> Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
> 

Podívejte se na příklad kódu .NET v [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kódování v reálném čase  

![Kódování v reálném čase](./media/live-streaming/live-encoding.svg)

Při použití kódování v reálném čase pomocí Media Services nakonfigurujte místní kodér pro kódování v reálném čase tak, aby jako informační kanál příspěvku do živé události odesílal video s jednou přenosovou rychlostí (pomocí protokolu RTMP nebo fragmentovaného MP4). By pak nastavíte živá událost tak, aby zakóduje této příchozí s jednou přenosovou rychlostí na datový proud stream [více přenosovými rychlostmi datový proud videa](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)a zpřístupňuje výstup pro doručování a přehrávání zařízení prostřednictvím protokolů, jako jsou MPEG-DASH, HLS, Smooth Streamování.

Použijete-li kódování v reálném čase, můžete poslat příspěvek kanálu pouze na rozlišení k rozlišení 1080p rychlostí rámec 30 snímků/druhé kodek H.264/AVC videa a AAC (AAC-LC, HE-AACv1 nebo HE-AACv2) zvukový kodek. Všimněte si, že předávací živé události může podporovat řešení až po 4 kB na 60 snímků za sekundu. Další podrobnosti najdete v článku popisujícím [porovnání typů živých událostí](live-event-types-comparison.md).

Rozlišení a přenosových rychlostí obsažené ve výstupu kodér služby live Encoding je určeno přednastavený kontext. Pokud se používá **standardní** live encoder (LiveEventEncodingType.Standard), pak bude *Default720p* přednastavení určuje sadu párů 6 rychlost řešení/bit mezi 720 p na 3.5Mbps dolů 192 p na 200 kb/s. Jinak, pokud se používá **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), pak bude *Default1080p* přednastavení určuje sadu párů 6 rychlost řešení/bit mezi 1080 p na 3.5Mbps až 180 p na 200 kb/s. Další informace najdete v tématu [Předvolby sytému](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování předvolbu, otevřete prosím lístek podpory prostřednictvím webu Azure portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva na 720p (Pokud se požaduje přednastavení kodér úrovně Standard za provozu) nebo na 1080p (Pokud se požaduje přednastavení Premium1080p live encoder) a maximálně 6 vrstvy.

## <a name="live-event-creation-options"></a>Možnosti vytvoření živé události

Při vytváření živá událost, můžete zadat následující možnosti:

* Protokol streamování pro živá událost (v současné době jsou podporovány protokoly RTMP nebo Smooth Streaming).<br/>Možnost protokolu nelze změnit během živá událost nebo jeho přidružené výstupů za běhu. Pokud požadujete různé protokoly, měli byste vytvořit samostatné živá událost pro každý protokol streamování.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které jsou povoleno ingestování videa tato živá událost. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nezadáte žádné IP adresy a neexistuje definice pravidla, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: Adresu IpV4 s 4 číslice, rozsah adres CIDR.
* Při vytváření události můžete nastavit automatické spouštění. <br/>Když automatické spuštění je nastavena na hodnotu true, živá událost se spustí po jeho vytvoření. Účtování začne Jakmile živá událost se spustí. Musíte explicitně volat Stop prostředku živá událost, která zastaví další fakturace. Alternativně můžete spustit událost, když budete chtít spustit streamování. 

    Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Živá událost ingestované adresy URL

Po vytvoření živé události můžete získat adresy URL ingestu, které poskytnete místní kodéru pro kódování v reálném čase. Kodér pro kódování v reálném čase tyto adresy URL používá ke vkládání živého proudu. Další informace najdete v tématu [doporučené místní kodéry](recommended-on-premises-live-encoders.md). 

Můžete použít buď nejednoduché adresy URL, nebo jednoduché adresy URL. 

> [!NOTE] 
> Pro adresu URL ingestování bude prediktivní nastavte režim "vlastní".

* Není jednoduché adrese URL

    Nejednoduché adresy URL představují v AMS verze 3 výchozí režim. Živou událost získáte potenciálně rychle, ale adresa URL ingestu je známá až při spuštění živé události. Pokud živou událost zastavíte nebo spustíte, adresa URL se změní. <br/>Nejednoduché adresy jsou užitečné v situacích, kdy koncový uživatel chce streamovat pomocí aplikace, která chce získat živou událost co nejdříve, a použití dynamické adresy URL ingestu nepředstavuje žádný problém.
* Jednoduché adrese URL

    Režim jednoduchých adres preferují velké mediální vysílače, které používají hardwarové kodéry vysílání a nechtějí znovu konfigurovat své kodéry při spuštění živé události. Chtějí předvídatelnou adresu URL ingestu, která se během doby nemění.
    
    Chcete-li určit tento režim nastavíte `vanityUrl` k `true` v okamžiku vytvoření (výchozí hodnota je `false`). Je také potřeba předat přístupového tokenu (`LiveEventInput.accessToken`) v okamžiku vytvoření. Můžete zadat hodnotu tokenu, aby se zabránilo náhodné token v adrese URL. Přístupový token musí být platný řetězec identifikátoru GUID (s nebo bez pomlček). Po nastavení režimu nelze aktualizovat.

    Přístupový token musí být jedinečný ve vašem datovém centru. Pokud vaše aplikace potřebuje používat jednoduché adrese URL, doporučujeme vždy vytváří nové instance identifikátoru GUID pro váš přístupový token (namísto opakovaného použití jakékoli existující identifikátor GUID). 

    Pomocí následující rozhraní API můžete povolit jednoduché adrese URL a nastavit přístupový token platný identifikátor GUID (například `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`):
    
    |Jazyk|Povolit jednoduché adrese URL|Nastavení přístupového tokenu|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |Rozhraní příkazového řádku|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Živé ingestování – pravidla pojmenování adresy URL

Řetězec *random* dále je 128bitové šestnáctkové číslo (skládající se z 32 znaků 0-9 a-f).<br/>
*Přístupový token* je nutné zadat pro pevnou adresu URL. Je nutné nastavit přístupový token řetězec je řetězec identifikátoru GUID platnou délku. <br/>
*Název streamu* Určuje název datového proudu pro konkrétní připojení. Název hodnoty datového proudu je obvykle přidány za kodér, že používáte.

#### <a name="non-vanity-url"></a>Není jednoduché adrese URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Jednoduché adrese URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Náhled adresy URL pro živé události

Jakmile **živá událost** spustí příjem příspěvků datového kanálu, můžete použít svůj koncový bod ve verzi preview pro zobrazení náhledu a ověření, že vám posíláme živého datového proudu před dalším publikování. Po kontrole, že datový proud ve verzi preview je dobré, vám pomůže Livestream uvolněte živého datového proudu pro doručení prostřednictvím jednoho nebo více (předem vytvořené) **koncové body streamování**. Chcete-li to provést, vytvořte nový [Live výstup](https://docs.microsoft.com/rest/api/media/liveoutputs) na **živá událost**. 

> [!IMPORTANT]
> Ujistěte se, že je video směřující do adresy URL náhledu před pokračováním!

## <a name="live-event-long-running-operations"></a>Živé události dlouho běžící operace

Podrobnosti najdete v tématu [dlouho běžící operace](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Živé výstupy

Jakmile se datový proud plyne do živá událost, streamování událostí můžete začít tak, že vytvoříte [Asset](https://docs.microsoft.com/rest/api/media/assets), [Live výstup](https://docs.microsoft.com/rest/api/media/liveoutputs), a [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators). Za výstupní archivní datový proud, který se ji dejte k dispozici se divákům prostřednictvím [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Live výstupů spuštění při vytvoření a přestanou při odstranění. Při odstranění výstupní Live nejsou odstraněním podkladových prostředků a obsahu v prostředku. 

Vztah mezi **živá událost** a jeho **Live výstupy** je podobná tradičním televizní vysílání, kterým kanál (**živá událost**) představuje konstantu datový proud videa a nahrávání (**Live výstup**) působí na určité časové segmentů (například večer novinky od 18:30:00 do 19:00:00). Televizi můžete nahrát pomocí videorekordéru. Ekvivalentní funkce u živých událostí se spravuje pomocí vlastnosti **ArchiveWindowLength**. Je formátu ISO 8601 časový interval doba trvání (například PTHH:MM:SS), která určuje kapacitu DVR a můžete nastavit na minimálně 3 minuty, které maximálně 25 hodin.

**Live výstup** objektu je jako pásku rekordéru, který bude zachytávat a poznamenejte si live stream do prostředku ve vašem účtu Media Services. Zaznamenaný obsah bude trvale uložit do účtu služby Azure Storage, který je připojený ke svému účtu, do kontejneru definované pomocí prostředků resource. **Live výstup** také umožňuje řídit některé vlastnosti odchozí živého datového proudu, jako je například kolik datového proudu se ukládají v záznamu archivu (například kapacita cloudového DVR) a určuje, jestli můžete spustit prohlížeče sledování živého datového proudu. Archiv na disku je do kruhové archivu "okno", který obsahuje pouze množství obsahu, který je zadán v **archiveWindowLength** vlastnost **Live výstup**. Obsah, který spadá mimo toto okno se automaticky zruší z kontejneru úložiště a nepůjde obnovit. Můžete vytvořit více **Live výstupy** (až tři maximální) na **živá událost** s nastaveními a archivovat různé délky.  

Pokud jste publikovali **Live výstup**společnosti **Asset** pomocí **Lokátor streamování**, **živá událost** (až do délky okna DVR) bude Pokračovat lze zobrazit až do vypršení platnosti nebo odstranění Lokátor streamování, podle toho, co nastane dřív.

Další informace najdete v tématu [pomocí cloudového DVR](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

[Živé streamování kurz](stream-live-tutorial-with-api.md)
