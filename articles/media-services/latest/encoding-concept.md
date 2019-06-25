---
title: Kódování v cloudu s využitím Media Services – Azure | Dokumentace Microsoftu
description: Toto téma popisuje proces kódování při použití služby Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b0a71e8b3ffff822521a23aafd6764bcce9bd4d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303936"
---
# <a name="encoding-with-media-services"></a>Kódování pomocí Media Services

Termín kódování v Media Services se vztahuje na proces převodu soubory, které obsahují digitální video nebo zvuk z jednoho standardního formátu do druhého, aby (a) zmenšení velikosti souborů nebo (b) vytváření formátu, který je kompatibilní s široké škále zařízení a aplikací. Tento proces se označuje také jako komprese videa, rozlišení nebo překódování. Najdete v článku [komprese dat](https://en.wikipedia.org/wiki/Data_compression) a [novinky kódování a překódování?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) Další informace o koncepty.

Videa jsou obvykle doručit do zařízení a aplikací pomocí [progresivní stahování](https://en.wikipedia.org/wiki/Progressive_download) nebo prostřednictvím [streamování s adaptivní přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* K doručování pomocí progresivního stahování, můžete použít Azure Media Services k převodu digitální mediální soubor (soubor mezzanine) do [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) soubor, který obsahuje video, které má zakódovány [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) kodeku, a zvuk, který má zakódovány [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) kodek. Tento soubor MP4 se zapíše do prostředku ve vašem účtu úložiště. Můžete použít rozhraní API služby Azure Storage nebo sady SDK (například [REST API pro Storage](../../storage/common/storage-rest-api-auth.md), [sady JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), nebo [sady .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) a stáhněte soubor přímo. Pokud jste vytvořili výstupní Asset s názvem konkrétní kontejner v úložišti, použijte toto umístění. V opačném případě můžete použít k Media Services [seznam adres URL kontejneru asset](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Pokud chcete připravit obsah pro doručování streamování s adaptivní přenosovou rychlostí, soubor mezzanine musí kódováním v několika přenosových rychlostí (nejvyšších po nejnižší). K zajištění řádné přechodu kvality, jak snížit přenosové rychlosti, takže je rozlišení videa. Výsledkem je takzvané kódování žebříku – tabulku rozlišení a přenosových rychlostí (viz [automaticky generované adaptivní žebříček přenosových rychlostí](autogen-bitrate-ladder.md)). Media Services můžete použít ke kódování mezzanine soubory na více přenosových rychlostí – přitom, získáte sadu souborů MP4 a přidružených datových proudů konfigurační soubory zapsány do prostředku ve vašem účtu úložiště. Pak můžete použít [dynamické balení](dynamic-packaging-overview.md) schopností ve službě Media Services k doručování videa prostřednictvím streamování protokolů, jako jsou [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) a [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). To je potřeba vytvořit [Lokátor streamování](streaming-locators-concept.md) a vytvořit odpovídající podporovaných protokolů, které je pak možné předat zařízení a aplikací založených na jejich schopnosti adresy URL pro streamování.

Následující diagram znázorňuje pracovní postup pro kódování na vyžádání s dynamickým vytvářením paketů.

![Dynamické balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Toto téma poskytuje pokyny o tom, jak zakódovat svůj obsah pomocí služby Media Services v3.

## <a name="transforms-and-jobs"></a>Transformace a úlohy

Ke kódování pomocí Media Services v3, je potřeba vytvořit [transformace](https://docs.microsoft.com/rest/api/media/transforms) a [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Transformací, která definuje nebudou tím správným kódování nastavení a výstupy; Úloha je instance předpisu. Další informace najdete v tématu [transformuje a úlohy](transforms-jobs-concept.md)

Kódování pomocí Media Services, použijete přednastavení kodér zjistit, jak by se měly zpracovat vstupními multimediálními soubory. Můžete například zadat rozlišení a/nebo počet zvukové kanály, které chcete v kódovaném obsahu. 

Můžete začít rychle s jedním z doporučených předdefinované předvolby založená na osvědčených postupech oboru nebo můžete vytvářet vlastní přednastavení cílit na konkrétní scénář nebo zařízení požadavky. Další informace najdete v tématu [kódovat s vlastní transformace](customize-encoder-presets-how-to.md). 

Počínaje lednem 2019 při kódování pomocí Media Encoder Standard na vytvářejí soubory MP4, je nový soubor .mpi generují a přidávají do výstupu Asset. Tento soubor MPI slouží pro zvýšení výkonu [dynamické balení](dynamic-packaging-overview.md) a streamování scénáře.

> [!NOTE]
> Nesmí změnit nebo odebrat soubor MPI či provést všechny závislosti ve své službě existence (nebo nemusíte) tyto souboru.

### <a name="creating-job-input-from-an-https-url"></a>Vytváření vstup úlohy z adresy URL HTTPS

Při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Jednu z možností je zadat adresu URL HTTPS jako vstup úlohy. Media Services v3 v současné době nepodporuje blokového kódování prostřednictvím adresy URL HTTPS přenosu. 

#### <a name="examples"></a>Příklady

* [Kódování z adresy URL protokolu HTTPS s využitím .NET](stream-files-dotnet-quickstart.md)
* [Kódování z adresy URL HTTPS pomocí REST](stream-files-tutorial-with-rest.md)
* [Kódování z adresy URL HTTPS pomocí rozhraní příkazového řádku](stream-files-cli-quickstart.md)
* [Kódování z adresy URL protokolu HTTPS s využitím Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Vytváření vstup úlohy z místního souboru

Vstupního videa mohou být uloženy jako majetek Media Service, v takovém případě třeba pouze vytvořit vstupní prostředek založený na souboru (uložená místně nebo ve službě Azure Blob storage). 

#### <a name="examples"></a>Příklady

[Kódování pomocí předdefinované předvolby místního souboru](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Vytváření vstup úlohy s oříznutím

Při kódování videa, můžete zadat také oříznout nebo oříznout zdrojového souboru a výstup, který obsahuje pouze požadovaná část vstupního videa. Tato funkce funguje s jakoukoli [transformace](https://docs.microsoft.com/rest/api/media/transforms) , který je sestaven buď pomocí [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) přednastavení, nebo [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) předvolby. 

Můžete použít k vytvoření [úlohy](https://docs.microsoft.com/rest/api/media/jobs/create) s klip videa na vyžádání nebo živý archiv (zaznamenané události). Vstup úlohy může být prostředek nebo adresu URL HTTPS.

> [!TIP]
> Pokud chcete Streamovat sublip videa bez reencoding videa, zvažte použití [předem filtrování manifesty s dynamické Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Příklady

Příklady:

* [Dílčí klip videa s využitím .NET](subclip-video-dotnet-howto.md)
* [Dílčí klip videa s využitím REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Předdefinované předvolby

Služba Media Services momentálně podporuje následující předdefinované kódovací předvolby:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) slouží k nastavení předdefinované předvolby pro kódování vstupního videa se kodér úrovně Standard. 

Tyto předvolby se aktuálně podporují:

- **EncoderNamedPreset.AACGoodQualityAudio** -vytvoří jeden soubor MP4 obsahující pouze stereo zvuk kódovaný v 192 kb/s.
- **EncoderNamedPreset.AdaptiveStreaming** (doporučeno). Další informace najdete v tématu [automatické generování přenosových](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -zpřístupňuje experimentální předvolbu kódování obsahu. Zadaný žádný vstupní obsah, se služba pokusí automaticky určit optimální počet vrstev, odpovídající s přenosovou rychlostí a nastavení řešení pro doručování pomocí adaptivního streamování. Základní algoritmy bude časem vyvíjet. Výstup bude obsahovat soubory MP4 s videa a zvuku prokládané. Další informace najdete v tématu [experimentální přednastavení kódování obsahu](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** -vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 1080p a platí až 360 p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 720p a platí až 360 p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereo AAC zvuku. Rozlišení začíná 480p a platí až 360 p.
- **EncoderNamedPreset.H264SingleBitrate1080p** – vytvoří soubor MP4 videa jsou zakódovány H.264 kodek 6750 kb/s a obrázek výšku 1080 pixelů, kde stereo zvuk jsou zakódovány kodek AAC-LC na 64 kb/s.
- **EncoderNamedPreset.H264SingleBitrate720p** – vytvoří soubor MP4 videa jsou zakódovány H.264 kodek 4500 kb/s a výška obrázku v pixelech 720, kde stereo zvuk jsou zakódovány kodek AAC-LC na 64 kb/s.
- **EncoderNamedPreset.H264SingleBitrateSD** – vytvoří soubor MP4 ve videu jsou zakódovány H.264 kodek 2200 kb/s a výška obrázku v pixelech 480, a stereo zvuk jsou zakódovány kodek AAC-LC na 64 kb/s.

Pokud chcete zobrazit aktuální seznam přednastavení, naleznete v tématu [předdefinované předvolby pro kódování videa](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Pokud chcete zobrazit, jak se používají na předvolby, podívejte se na [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) popisuje nastavení, která se použije při kódování vstupního videa se kodér úrovně Standard. Použijte přednastavení při přizpůsobování přednastavení transformace. 

#### <a name="considerations"></a>Požadavky

Při vytváření vlastní předvolby, platí následující aspekty:

- Všechny hodnoty pro výšku a šířku na AVC obsahu musí být násobkem 4.
- V Azure Media Services v3 kódování přenosových rychlostí jsou všechny bity za sekundu. Tím se liší od přednastavení pomocí rozhraní API v2, který používá kilobitů za sekundu za jednotku. Například pokud přenosovými rychlostmi ve verzi v2 byl zadán jako 128 (kilobitů za sekundu), ve verzi 3 to se nastavuje na 128000 (bitů za sekundu).

### <a name="customizing-presets"></a>Přizpůsobení předvoleb

Služba Media Services plně podporuje všechny hodnoty v předvolbách pro splnění potřeb kódování a požadavky na přizpůsobení. Příklady, které ukazují, jak přizpůsobit kódovací předvolby naleznete v tématu:

#### <a name="examples"></a>Příklady

- [Přizpůsobení předvoleb pomocí .NET](customize-encoder-presets-how-to.md)
- [Přizpůsobení předvoleb pomocí rozhraní příkazového řádku](custom-preset-cli-howto.md)
- [Přizpůsobení předvoleb pomocí REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Přednastavení schématu

V Media Services v3 přednastavení jsou silného typu entity v samotné rozhraní API. Můžete najít definice "schéma" pro tyto objekty v [specifikace Openapi (nebo Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Můžete také zobrazit výchozí definice (například **StandardEncoderPreset**) v [rozhraní REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (nebo jiné služby Media Services v3 SDK referenční dokumentaci).

## <a name="scaling-encoding-in-v3"></a>Škálování kódování ve verzi 3

Škálování zpracování médií, naleznete v tématu [škálování pomocí rozhraní příkazového řádku](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

* [Nahrávání, kódování a streamování pomocí služby Media Services](stream-files-tutorial-with-api.md)
* [Kódování z adresy URL HTTPS pomocí předdefinované předvolby](job-input-from-http-how-to.md)
* [Kódování pomocí předdefinované předvolby místního souboru](job-input-from-local-file-how-to.md)
* [Vytvoření vlastního přednastaveny tak, aby cílit na konkrétní scénář nebo zařízení požadavky](customize-encoder-presets-how-to.md)