---
title: Kódování videa a zvuku pomocí Media Services
description: Tento článek vysvětluje, jak kódovat video a zvuk pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6a486057a265b02ce30059940c8c98837ec43f8e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617637"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Kódování videa a zvuku pomocí Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Termín kódování v Media Services se vztahuje na proces převodu souborů obsahujících digitální video nebo zvuk z jednoho standardního formátu na jiný, s účelem (a) zmenšování velikosti souborů a/nebo (b), která vytváří formát kompatibilní s širokou škálou zařízení a aplikací. Tento proces je také označován jako komprese videa nebo překódování. Další diskuzi o konceptech najdete v tématu [komprese dat](https://en.wikipedia.org/wiki/Data_compression) a [co je kódování a překódování](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) .

Videa se většinou doručují do zařízení a aplikací pomocí [postupného stahování](https://en.wikipedia.org/wiki/Progressive_download) nebo [streamování s adaptivní přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Media Services nevyúčtuje zrušené nebo chybové úlohy. Například úloha, která dosáhla pokroku 50% a je zrušená, se neúčtuje za 50% minut úlohy. Účtují se vám pouze dokončené úlohy.

* Aby bylo možné doručit progresivním stažením, můžete použít Azure Media Services k převodu digitálního mediálního souboru (Mezzanine) na soubor [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) , který obsahuje video kódované pomocí kodeku [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) , a zvuk kódovaný pomocí kodeku [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) . Tento soubor MP4 se zapisuje do Assetu v účtu úložiště. Můžete použít rozhraní API Azure Storage nebo sady SDK (například [úložiště REST API](../../storage/common/storage-rest-api-auth.md) nebo [sadu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) a stáhnout soubor přímo. Pokud jste vytvořili výstupní Asset s konkrétním názvem kontejneru v úložišti, použijte toto umístění. V opačném případě můžete použít Media Services k [vypsání adres URL kontejneru assetů](/rest/api/media/assets/listcontainersas). 
* Aby bylo možné připravit obsah pro doručování streamování s adaptivní přenosovou rychlostí, musí být soubor Mezzanine kódovaný s více přenosovými rychlostmi (vysoká až nízká). Aby se zajistil řádný přechod kvality, rozlišení videa se sníží, protože se sníží rychlost. Výsledkem je, že se jako kódovací žebřík používá tabulka rozlišení a přenosové rychlosti (viz [automaticky generovaná žebřík s adaptivní přenosovou rychlostí](autogen-bitrate-ladder.md)). Můžete použít Media Services ke kódování souborů Mezzanine s více přenosovými rychlostmi. V takovém případě získáte sadu souborů MP4 a přidružených konfiguračních souborů streamování zapsaných do Assetu v účtu úložiště. Pak můžete použít funkci [dynamického balení](dynamic-packaging-overview.md) v Media Services k doručování videa prostřednictvím protokolů streamování, jako jsou [MPEG-spojovníky](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) a [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). To vyžaduje, abyste vytvořili [Lokátor streamování](streaming-locators-concept.md) a vytvořili adresy URL streamování odpovídající podporovaným protokolům, které se pak dají přesměrovat na zařízení nebo aplikace na základě jejich schopností.

Následující diagram znázorňuje pracovní postup pro kódování na vyžádání s dynamickým balením.

![Pracovní postup pro kódování na vyžádání s dynamickým balením](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

V tomto tématu najdete pokyny k zakódování obsahu pomocí Media Services V3.

## <a name="transforms-and-jobs"></a>Transformace a úlohy

Pro kódování pomocí Media Services V3 potřebujete vytvořit [transformaci](/rest/api/media/transforms) a [úlohu](/rest/api/media/jobs). Transformace definuje recept pro vaše nastavení a výstupy kódování; úloha je instancí receptury. Další informace najdete v tématu [Transformace a úlohy](transforms-jobs-concept.md).

Při kódování pomocí Media Services použijte předvolby k informování kodéru, jak mají být zpracovány vstupní mediální soubory. V Media Services V3 použijete ke kódování souborů Standard Encoder. Můžete například určit rozlišení videa nebo počet zvukových kanálů, které chcete v zakódovaném obsahu.

Můžete rychle začít s jedním z doporučených předplatných, které jsou založené na osvědčených postupech, nebo si můžete zvolit vytvoření vlastní předvolby, která bude cílit na konkrétní scénář nebo požadavky na zařízení. Další informace naleznete v tématu [kódování s vlastní transformací](customize-encoder-presets-how-to.md).

Od ledna 2019 se při kódování se standardem Encoder vytvoří soubory MP4 a vytvoří se nový soubor. MPI, který se přidá do výstupního prostředku. Tento soubor MPI má za cíl zlepšit výkon pro [dynamické balení](dynamic-packaging-overview.md) a streamování.

> [!NOTE]
> Soubor MPI byste neměli upravovat ani odebírat nebo v rámci služby nemusíte mít žádnou závislost na existenci (nebo ne) takového souboru.

### <a name="creating-job-input-from-an-https-url"></a>Vytváření vstupu úlohy z adresy URL HTTPS

Když odešlete úlohy pro zpracování videí, je nutné sdělit Media Services, kde najít vstupní video. Jednou z možností je určit adresu URL HTTPS jako vstup úlohy. V současné době Media Services V3 nepodporuje kódování bloků přenosu prostřednictvím adres URL protokolu HTTPS.

#### <a name="examples"></a>Příklady

* [Kódování z adresy URL HTTPS pomocí .NET](stream-files-dotnet-quickstart.md)
* [Kódování z adresy URL HTTPS pomocí REST](stream-files-tutorial-with-rest.md)
* [Kódování z adresy URL HTTPS pomocí rozhraní příkazového řádku](stream-files-cli-quickstart.md)
* [Kódování z adresy URL HTTPS pomocí Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Vytváření vstupu úlohy z místního souboru

Vstupní video se může ukládat jako Asset služby Media Service. v takovém případě vytvoříte vstupní Asset založený na souboru (uložený místně nebo v úložišti objektů BLOB v Azure).

#### <a name="examples"></a>Příklady

[Kódování místního souboru pomocí vestavěných přednastavení](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Vytváření vstupu úlohy pomocí dílčího výstřižku

Při kódování videa můžete určit, že se má zdrojový soubor také oříznout nebo vystřihnout a vytvořit výstup, který má pouze požadovanou část vstupního videa. Tato funkce funguje s libovolnou [transformací](/rest/api/media/transforms) , která je sestavená buď pomocí [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) nebo předvoleb [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) .

Můžete zadat, chcete-li vytvořit [úlohu](/rest/api/media/jobs/create) s jedním klipem na vyžádání nebo v živém archivu (zaznamenaná událost). Vstup úlohy může být Asset nebo adresa URL protokolu HTTPS.

> [!TIP]
> Pokud chcete streamovat sublip videa bez opětovného kódování videa, zvažte použití [předem filtrovaných manifestů s dynamickým balíčkem](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Příklady

Viz příklady:

* [Vytvoření dílčího klipu videa s využitím .NET](subclip-video-dotnet-howto.md)
* [Vytvoření dílčího klipu videa s REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Vestavěná přednastavení

Media Services podporuje následující předdefinované předvolby kódování:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) se používá k nastavení integrované předvolby pro kódování vstupního videa pomocí kodéru Standard.

V současné době jsou podporovány následující předdefinované předvolby:

- **EncoderNamedPreset. AACGoodQualityAudio**: vytvoří jeden soubor MP4 obsahující pouze stereofonní zvuk kódovaný v 192 KB/s.
- **EncoderNamedPreset. AdaptiveStreaming** (doporučeno): Tato podpora umožňuje kódování H. 264 s adaptivní přenosovou rychlostí. Další informace najdete v tématu [Automatické generování žebříku přenosové rychlosti](autogen-bitrate-ladder.md).
- **EncoderNamerPreset. H265AdaptiveStreaming** : podobná předvolbě AdaptiveStreaming, ale používá kodek HEVC (H. 265). Vytvoří sadu souborů MP4 zarovnaných pomocí skupinu GOP s videem H. 265 a stereofonním zvukem AAC. Automaticky generuje žebřík s přenosovou rychlostí na základě rozlišení vstupu, přenosové rychlosti a snímkové frekvence. Automaticky vygenerované předvolby nikdy nepřekročí vstupní rozlišení. Například pokud je vstup ve formátu 720p, výstup bude ve formátu 720p nejlepší.
- **EncoderNamedPreset. ContentAwareEncoding**: zpřístupňuje přednastavení pro kódování s ohledem na obsah H. 264. Vzhledem k jakémukoli vstupnímu obsahu se služba pokusí automaticky určit optimální počet vrstev a odpovídající rychlost a nastavení rozlišení pro doručování pomocí adaptivního streamování. Základní algoritmy se budou dál vyvíjet v průběhu času. Výstup bude obsahovat soubory MP4 se zakládaným videem a zvukem. Další informace najdete v tématu [kódování s ohledem na obsah](content-aware-encoding.md).
- **EncoderNamedPreset. H265ContentAwareEncoding**: zpřístupňuje přednastavení pro kódování s podporou obsahu HEVC (H. 265). Vytvoří sadu rychlostmi zarovnaných k skupinu GOP pomocí kódování s podporou obsahu. Vzhledem k jakémukoli vstupnímu obsahu služba provádí počáteční odlehčenou analýzu vstupního obsahu a výsledky používá k určení optimálního počtu vrstev, vhodné rychlosti a nastavení rozlišení pro doručování pomocí adaptivního streamování. Tato předvolba je zvláště platná pro videa s nízkou a střední složitostí, kde výstupní soubory budou s nižšími rychlostmi přenosů, ale i kvalita, která uživatelům nabízí dobré prostředí. Výstup bude obsahovat soubory MP4 se zakládaným videem a zvukem.
  > [!NOTE]
  > Ujistěte se, že používáte **ContentAwareEncoding** , který není ContentAwareEncodingExperimental, který je teď zastaralý.

- **EncoderNamedPreset. H264MultipleBitrate1080p**: vytvoří sadu osmi souborů MP4 zarovnaných na skupinu GOP, od 6000 do 400 KB/s a stereofonního zvuku AAC. Řešení začíná v 1080p a přejde do 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: vytvoří sadu šesti souborů MP4 zarovnaných na skupinu GOP, od 3400 do 400 KB/s a stereofonního zvuku AAC. Řešení začíná ve formátu 720p a přejde do 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: vytvoří sadu pěti souborů MP4 zarovnaných na skupinu GOP, od 1600 do 400 KB/s a stereofonního zvuku AAC. Řešení začíná na 480p a přejde do 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: vytvoří soubor MP4, kde video je zakódováno pomocí kodeku H. 264 v 6750 kbps a výšce obrázku 1080 pixelů a stereofonní zvuk je kódovaný pomocí kodeku AAC-LC při 64 kB/s.
- **EncoderNamedPreset. H264SingleBitrate720p**: vytvoří soubor MP4, kde video je zakódováno pomocí kodeku H. 264 v 4500 kbps a výšce obrázku 720 pixelů a stereofonní zvuk je kódovaný pomocí kodeku AAC-LC při 64 kB/s.
- **EncoderNamedPreset. H264SingleBitrateSD**: vytvoří soubor MP4, kde video je zakódováno pomocí kodeku H. 264 v 2200 kbps a výšce obrázku 480 pixelů a stereofonní zvuk je kódovaný pomocí kodeku AAC-LC při 64 kB/s.
- **EncoderNamedPreset. H265SingleBitrate720P**: vytvoří soubor MP4, kde je video zakódováno pomocí kodeku HEVC (H. 265) v 1800 kbps a výšce obrázku 720 pixelů a stereo zvuk je kódovaný pomocí kodeku AAC-LC v 128 kb/s.
- **EncoderNamedPreset. H265SingleBitrate1080p**: vytvoří soubor MP4, kde je video zakódováno pomocí kodeku HEVC (H. 265) v 3500 kbps a výšce obrázku 1080 pixelů a stereo zvuk je kódovaný pomocí kodeku AAC-LC v 128 kb/s.
- **EncoderNamedPreset. H265SingleBitrate4K**: vytvoří soubor MP4, kde je video zakódováno pomocí kodeku HEVC (H. 265) v 9500 kbps a výšce obrázku 2160 pixelů a stereo zvuk je kódovaný pomocí kodeku AAC-LC v 128 kb/s.

Pokud chcete zobrazit seznam nejaktuálnějších nastavení, přečtěte si téma [integrované předvolby, které se použijí pro kódování videí](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Pokud chcete zjistit, jak se používají předvolby, přečtěte si téma [nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) popisuje nastavení, která se použijí při kódování vstupního videa pomocí kodéru Standard. Tuto předvolbu použijte při přizpůsobení přednastavení transformace.

#### <a name="considerations"></a>Požadavky

Při vytváření vlastních přednastavení platí následující požadavky:

- Všechny hodnoty pro výšku a šířku v obsahu AVC musí být násobkem čtyř.
- V Azure Media Services V3 jsou všechny přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi rozhraními API v2, která jako jednotku používala kilobity za sekundu. Pokud je například přenosová rychlost v v2 zadaná jako 128 (kilobit/s), ve verzi V3 by se nastavila na 128000 (bity za sekundu).

### <a name="customizing-presets"></a>Přizpůsobení předvoleb

Media Services plně podporuje přizpůsobení všech hodnot v předvolbách, aby splňovaly vaše specifické požadavky na kódování a požadavky. Příklady, které ukazují, jak přizpůsobit předvolby kodéru, najdete v následujícím seznamu:

#### <a name="examples"></a>Příklady

- [Přizpůsobení přednastavení pomocí .NET](customize-encoder-presets-how-to.md)
- [Přizpůsobení přednastavení pomocí rozhraní příkazového řádku](custom-preset-cli-howto.md)
- [Přizpůsobení předvoleb pomocí REST](custom-preset-rest-howto.md)


## <a name="preset-schema"></a>Předdefinované schéma

V Media Services V3 jsou předvolby silně typované entity v rozhraní API. Definici "schématu" těchto objektů najdete v tématu [Open API Specification (nebo Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Přednastavené definice (například **StandardEncoderPreset**) můžete zobrazit také v [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (nebo v jiné referenční dokumentaci sady Media Services V3 SDK).

## <a name="scaling-encoding-in-v3"></a>Škálování kódování ve verzi 3

Škálování zpracování médií najdete v tématu [škálování pomocí](media-reserved-units-cli-how-to.md)rozhraní příkazového řádku.
Pro účty vytvořené v rámci verze **2020-05-01** rozhraní API nebo prostřednictvím Azure Portal se už nevyžadují škálování a rezervované jednotky médií. Škálování bude automaticky zpracováno službou interně.

## <a name="billing"></a>Fakturace

Media Services nevyúčtuje zrušené nebo chybové úlohy. Například úloha, která dosáhla pokroku 50% a je zrušená, se neúčtuje za 50% minut úlohy. Účtují se vám pouze dokončené úlohy.

Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

* [Nahrávání, kódování a streamování pomocí Media Services](stream-files-tutorial-with-api.md).
* [Kódování z adresy URL protokolu HTTPS pomocí integrovaných přednastavení](job-input-from-http-how-to.md).
* [Kódování místního souboru pomocí vestavěných přednastavení](job-input-from-local-file-how-to.md).
* [Vytvořte vlastní předvolbu, která bude cílit na konkrétní scénář nebo požadavky na zařízení](customize-encoder-presets-how-to.md).
