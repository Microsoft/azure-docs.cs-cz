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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: cce3ea06ebd7d3469dad14e491124f81567610ea
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894047"
---
# <a name="live-events-and-live-outputs"></a>Živé události a výstupy

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Pokud chcete nakonfigurovat živě streamovaných událostí v Media Services v3, je potřeba pochopit principy probírané v tomto článku:

* [Události v reálném čase](#live-events)
* Live typy událostí
* Porovnání typů živé události
* [Možnosti vytvoření živé události](#live-event-creation-options)
* [Živá událost ingestované adresy URL](#live-event-ingest-urls)
* [Náhled adresy URL pro živé události](#live-event-preview-url)
* [Live výstupy](#live-outputs).

## <a name="live-events"></a>Živé události

[Živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za příjem a zpracování živého videa informačních kanálů. Při vytváření živá událost se vytvoří, můžete použít k odesílání živě signál z vzdálený kodér vstupní koncový bod. Vzdáleném kodér služby live Encoding odešle informační kanál k příspěvek vstupní koncový bod buď pomocí [RTMP](https://www.adobe.com/devnet/rtmp.html) nebo [technologie Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protocol (fragmentovaný soubor MP4). Pro technologii Smooth Streaming ingestování, jsou podporovaná schémata URL `http://` nebo `https://`. Protokol ingestování RTMP, jsou podporovaná schémata URL `rtmp://` nebo `rtmps://`. 

## <a name="live-event-types"></a>Live typy událostí

A [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: Předávací tak pro živé kódování. 

### <a name="pass-through"></a>Průchod

![Předávací](./media/live-streaming/pass-through.png)

Při použití předávací **živá událost**, můžete spoléhat na vaše místní kodér služby live Encoding pro vygenerování více datový proud videa s přenosovou rychlostí a odeslat, že jako příspěvek kanálu pro živá událost (pomocí protokolu RTMP nebo fragmentovaný soubor MP4). Živá událost se potom provede prostřednictvím příchozí datové proudy videa bez dalšího zpracování. Předávací Livestream je optimalizovaný pro dlouho běžící živě přenášené události nebo 24 × 365 lineární živé streamování. Při vytváření tohoto typu živá událost, zadejte None (LiveEventEncodingType.None).

Můžete odeslat příspěvek kanálu na rozlišení až 4 kB a v objektu frame míra 60 snímků za sekundu s H.264/AVC nebo H.265/HEVC video kodeků a AAC (AAC-LC, HE-AACv1 nebo HE-AACv2) zvukový kodek.  Zobrazit [živá událost typy porovnání](live-event-types-comparison.md) , kde najdete další podrobnosti.

> [!NOTE]
> Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
> 

Podívejte se na příklad kódu .NET v [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Kódování v reálném čase  

![živé kódování](./media/live-streaming/live-encoding.png)

Pokud používáte živé kódování pomocí Media Services, nakonfigurujete by vaše místní kodér služby live Encoding odesílat videa s jednou přenosovou rychlostí jako příspěvek informačního kanálu živá událost (pomocí protokolu RTMP nebo fragmentovaný soubor Mp4). Živá událost kóduje této příchozí s jednou přenosovou rychlostí na datový proud stream [více přenosovými rychlostmi datový proud videa](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), zpřístupní pro doručení pro přehrávání zařízení prostřednictvím protokolů, jako jsou MPEG-DASH, HLS a Smooth Streaming. Při vytváření tohoto typu živá událost, zadejte jako typ kódování **standardní** (LiveEventEncodingType.Standard).

Můžete odeslat příspěvek kanálu na až rozlišení 1080 p rychlostí rámec 30 snímků/druhé kodek H.264/AVC videa a AAC (AAC-LC, HE-AACv1 nebo HE-AACv2) zvukový kodek. Zobrazit [živá událost typy porovnání](live-event-types-comparison.md) , kde najdete další podrobnosti.

## <a name="live-event-creation-options"></a>Možnosti vytvoření živé události

Při vytváření živá událost, můžete zadat následující možnosti:

* Protokol streamování pro živá událost (v současné době jsou podporovány protokoly RTMP nebo Smooth Streaming).<br/>Možnost protokolu nelze změnit během živá událost nebo jeho přidružené výstupů za běhu. Pokud požadujete různé protokoly, měli byste vytvořit samostatné živá událost pro každý protokol streamování.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které jsou povoleno ingestování videa tato živá událost. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nezadáte žádné IP adresy a neexistuje definice pravidla, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: Adresu IpV4 s 4 číslice, rozsah adres CIDR.
* Při vytváření události můžete nastavit automatické spouštění. <br/>Když automatické spuštění je nastavena na hodnotu true, živá událost se spustí po jeho vytvoření. Účtování začne Jakmile živá událost se spustí. Musíte explicitně volat Stop prostředku živá událost, která zastaví další fakturace. Alternativně můžete spustit událost, když budete chtít spustit streamování. 

    Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Živá událost ingestované adresy URL

Po vytvoření živá událost můžete získat ingestované adresy URL, které poskytnete kodéru místně. Live encoder používá tyto adresy URL ke vkládání živého proudu. Další informace najdete v tématu [doporučené místní kodéry](recommended-on-premises-live-encoders.md). 

Můžete použít buď není jednoduché adresy URL nebo jednoduché adresy URL. 

* Není jednoduché adrese URL

    Non jednoduché adrese URL je výchozí režim ve verzi 3 AMS. Potenciálně rychle živá událost ale adresa URL ingestu je znám pouze při spuštění živé události. Pokud jste zastavit nebo spustit živá událost se změní adresa URL. <br/>Non-individuální je užitečné v situacích, když koncový uživatel chce Streamovat pomocí aplikace, pokud aplikace chce získat živou událost co nejdříve a s dynamickým adresa URL ingestu nepředstavuje žádný problém.
* Jednoduché adrese URL

    Režim individuální preferují velké mediální vysílání, kteří používají hardwaru vysílání kodérů a nechcete, aby znovu konfigurovat jejich kodérů při jejich spuštění živá událost. Chtějí prediktivní ingestování adresu URL, která se nezmění v čase.

> [!NOTE] 
> Pro adresu URL ingestování bude prediktivní budete muset použít režim "vlastní" a předat přístupového tokenu (aby se zabránilo náhodné token v adrese URL).

### <a name="live-ingest-url-naming-rules"></a>Živé ingestování – pravidla pojmenování adresy URL

*Náhodné* níže uvedeného řetězce je 128-bit šestnáctkovým číslem (který se skládá z 32 znaky 0-9-f).<br/>
*Přístupový token* níže je nutné zadat pro pevnou adresu URL. Je také šestnáctkovým číslem 128 bitů.

#### <a name="non-vanity-url"></a>Není jednoduché adrese URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>Jednoduché adrese URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Technologie Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>Náhled adresy URL pro živé události

Jakmile **živá událost** spustí příjem příspěvků datového kanálu, můžete použít svůj koncový bod ve verzi preview pro zobrazení náhledu a ověření, že vám posíláme živého datového proudu před dalším publikování. Po kontrole, že datový proud ve verzi preview je dobré, vám pomůže Livestream uvolněte živého datového proudu pro doručení prostřednictvím jednoho nebo více (předem vytvořené) **koncové body streamování**. Chcete-li to provést, vytvořte nový [Live výstup](https://docs.microsoft.com/rest/api/media/liveoutputs) na **živá událost**. 

> [!IMPORTANT]
> Ujistěte se, že je video směřující do adresy URL náhledu před pokračováním!

## <a name="live-outputs"></a>Živé výstupy

Jakmile se datový proud plyne do živá událost, streamování událostí můžete začít tak, že vytvoříte [Asset](https://docs.microsoft.com/rest/api/media/assets), [Live výstup](https://docs.microsoft.com/rest/api/media/liveoutputs), a [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators). Za výstupní archivní datový proud, který se ji dejte k dispozici se divákům prostřednictvím [koncový bod streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Live výstupů spuštění při vytvoření a přestanou při odstranění. Při odstranění výstupní Live nejsou odstraněním podkladových prostředků a obsahu v prostředku. 

Vztah mezi **živá událost** a jeho **Live výstupy** je podobná tradičním televizní vysílání, kterým kanál (**živá událost**) představuje konstantu datový proud videa a nahrávání (**Live výstup**) působí na určité časové segmentů (například večer novinky od 18:30:00 do 19:00:00). Můžete zaznamenat televizoru pomocí záznamu pro digitální Video (DVR) – ekvivalentní funkce v živé události se spravuje přes **ArchiveWindowLength** vlastnost. Je formátu ISO 8601 časový interval doba trvání (například PTHH:MM:SS), která určuje kapacitu DVR a můžete nastavit na minimálně 3 minuty, které maximálně 25 hodin.

**Live výstup** objektu je jako pásku rekordéru, který bude zachytávat a poznamenejte si live stream do prostředku ve vašem účtu Media Services. Zaznamenaný obsah bude trvale uložit do účtu služby Azure Storage, který je připojený ke svému účtu, do kontejneru definované pomocí prostředků resource. **Live výstup** také umožňuje řídit některé vlastnosti odchozí živého datového proudu, jako je například kolik datového proudu se ukládají v záznamu archivu (například kapacita cloudového DVR) a určuje, jestli můžete spustit prohlížeče sledování živého datového proudu. Archiv na disku je do kruhové archivu "okno", který obsahuje pouze množství obsahu, který je zadán v **archiveWindowLength** vlastnost **Live výstup**. Obsah, který spadá mimo toto okno se automaticky zruší z kontejneru úložiště a nepůjde obnovit. Můžete vytvořit více **Live výstupy** (až tři maximální) na **živá událost** s nastaveními a archivovat různé délky.  

Pokud jste publikovali **Live výstup**společnosti **Asset** pomocí **Lokátor streamování**, **živá událost** (až do délky okna DVR) bude Pokračovat lze zobrazit až do vypršení platnosti nebo odstranění Lokátor streamování, podle toho, co nastane dřív.

Další informace najdete v tématu [pomocí cloudového DVR](live-event-cloud-dvr.md).

## <a name="next-steps"></a>Další postup

- [Streamování události v reálném čase](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)
