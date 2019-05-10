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
ms.date: 05/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 937a032bffbad4e8a7d737360aa140e59760f8e2
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472440"
---
# <a name="encoding-with-media-services"></a>Kódování pomocí Media Services

Azure Media Services umožňuje má kódovat do souborů MP4 vysoké kvality digitální mediální soubory, tak svůj obsah můžete přehrát na širokou škálu prohlížečů a zařízení. Úspěšné úlohy kódování Media Services vytvoří výstupní Asset se sadou s adaptivní přenosovou rychlostí soubory MP4 rychlostmi a streamování konfigurační soubory. Konfigurační soubory zahrnují .ism, .ismc, .mpi a další soubory, které byste neměli měnit. Po dokončení úlohy kódování můžete využít výhod [dynamické balení](dynamic-packaging-overview.md) a spustit streamování.

Chcete-li videa ve výstupu prostředek k dispozici pro klienty pro přehrávání, je nutné vytvořit **Lokátor streamování** a vytvoření adresy URL pro streamování. Potom podle formátu určeného v manifestu, vaši klienti datový proud obdrželi v protokolu, které jste vybrali.

Následující diagram znázorňuje streamování na vyžádání s dynamickým vytvářením paketů pracovního postupu.

![Dynamické balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Toto téma poskytuje pokyny o tom, jak zakódovat svůj obsah pomocí služby Media Services v3.

## <a name="transforms-and-jobs"></a>Transformace a úlohy

Ke kódování pomocí Media Services v3, je potřeba vytvořit [transformace](https://docs.microsoft.com/rest/api/media/transforms) a [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Transformace definuje předpisu pro kódování nastavení a výstupů a úloha je instance předpisu. Další informace najdete v tématu [transformuje a úlohy](transforms-jobs-concept.md)

Kódování pomocí Media Services, použijete přednastavení kodér zjistit, jak by se měly zpracovat vstupními multimediálními soubory. Můžete například zadat rozlišení a/nebo počet zvukové kanály, které chcete v kódovaném obsahu. 

Můžete začít rychle s jedním z doporučených předdefinované předvolby založená na osvědčených postupech oboru nebo můžete vytvářet vlastní přednastavení cílit na konkrétní scénář nebo zařízení požadavky. Další informace najdete v tématu [kódovat s vlastní transformace](customize-encoder-presets-how-to.md). 

Počínaje lednem 2019 při kódování pomocí Media Encoder Standard na vytvářejí soubory MP4, je nový soubor .mpi generují a přidávají do výstupu Asset. Tento soubor MPI slouží pro zvýšení výkonu [dynamické balení](dynamic-packaging-overview.md) a streamování scénáře.

> [!NOTE]
> Nesmí změnit nebo odebrat soubor MPI či provést všechny závislosti ve své službě existence (nebo nemusíte) tyto souboru.

## <a name="built-in-presets"></a>Předdefinované předvolby

Služba Media Services momentálně podporuje následující předdefinované kódovací předvolby:  

### <a name="builtinstandardencoderpreset-preset"></a>Přednastavení BuiltInStandardEncoderPreset

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

### <a name="standardencoderpreset-preset"></a>Přednastavení StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) popisuje nastavení, která se použije při kódování vstupního videa se kodér úrovně Standard. Použijte přednastavení při přizpůsobování přednastavení transformace. 

#### <a name="considerations"></a>Požadavky

Při vytváření vlastní předvolby, platí následující aspekty:

- Všechny hodnoty pro výšku a šířku na AVC obsahu musí být násobkem 4.
- V Azure Media Services v3 kódování přenosových rychlostí jsou všechny bity za sekundu. Tím se liší od přednastavení pomocí rozhraní API v2, který používá kilobitů za sekundu za jednotku. Například pokud přenosovými rychlostmi ve verzi v2 byl zadán jako 128 (kilobitů za sekundu), ve verzi 3 to se nastavuje na 128000 (bitů za sekundu).

#### <a name="examples"></a>Příklady

Služba Media Services plně podporuje všechny hodnoty v předvolbách pro splnění potřeb kódování a požadavky na přizpůsobení. Příklady, které ukazují, jak přizpůsobit kódovací předvolby naleznete v tématu:

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

* [Kódování z adresy URL HTTPS pomocí předdefinované předvolby](job-input-from-http-how-to.md)
* [Kódování pomocí předdefinované předvolby místního souboru](job-input-from-local-file-how-to.md)
* [Vytvoření vlastního přednastaveny tak, aby cílit na konkrétní scénář nebo zařízení požadavky](customize-encoder-presets-how-to.md)
* [Nahrávání, kódování a streamování pomocí služby Media Services](stream-files-tutorial-with-api.md)
