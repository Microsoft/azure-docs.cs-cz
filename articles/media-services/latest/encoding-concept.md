---
title: Kódování videa a zvuku pomocí mediálních služeb
titleSuffix: Azure Media Services
description: Tento článek vysvětluje kódování videa a zvuku pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366584"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Kódování videa a zvuku pomocí mediálních služeb

Termín kódování ve službě Media Services se vztahuje na proces převodu souborů obsahujících digitální video a/nebo zvuk z jednoho standardního formátu do druhého za účelem (a) zmenšení velikosti souborů a/nebo (b) vytvoření formátu, který je kompatibilní s širokou škálou zařízení a aplikací. Tento proces se také označuje jako komprese videa nebo překódování. Další informace o konceptech naleznete v [tématech Komprese dat](https://en.wikipedia.org/wiki/Data_compression) a [Co je kódování a překódování?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

Videa se obvykle dodávají do zařízení a aplikací [postupným stahováním](https://en.wikipedia.org/wiki/Progressive_download) nebo [adaptivním datovým tokem](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Služba Media Services neúčtuje za zrušené nebo chybované úlohy. Například úloha, která dosáhla 50% pokroku a je zrušena, se neúčtuje 50 % minut úlohy. Poplatky vám budou účtovány pouze za dokončené úlohy.

* Chcete-li dodat postupným stahováním, můžete pomocí služby Azure Media Services převést soubor digitálních médií (mezanin) na soubor [MP4,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) který obsahuje video, které bylo zakódováno s kodekem [H.264,](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) a zvuk, který byl kódován kodekem [AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Tento soubor MP4 se zapíše do datového zdroje ve vašem účtu úložiště. K přímému stažení souboru můžete použít rozhraní API úložiště Azure nebo sady SDK (například [rozhraní REST API úložiště](../../storage/common/storage-rest-api-auth.md) nebo sada [.NET SDK).](../../storage/blobs/storage-quickstart-blobs-dotnet.md) Pokud jste vytvořili výstupní prostředek s konkrétním názvem kontejneru v úložišti, použijte toto umístění. V opačném případě můžete pomocí služby Media Services [vypsat adresy URL kontejneru datových zdrojů](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Chcete-li připravit obsah pro doručení adaptivní datový tok streamování, mezipatro soubor musí být kódován na více datových toky (od nejvyšší po nízké). Aby byl zajištěn ladný přechod kvality, sníží se rozlišení videa, protože se sníží přenosový tok. Výsledkem je takzvaný kódovací žebřík – tabulka rozlišení a datových toků (viz [automaticky generovaný adaptivní datový žebřík).](autogen-bitrate-ladder.md) Pomocí služby Media Services můžete kódovat mezaninové soubory při více přenosových rychlostech. Přitom získáte sadu souborů MP4 a přidružených konfiguračních souborů streamování zapsaných do datového zdroje ve vašem účtu úložiště. Potom můžete použít [funkci Dynamické balení](dynamic-packaging-overview.md) ve službě Media Services k doručování videa prostřednictvím protokolů streamování, jako jsou [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) a [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). To vyžaduje, abyste vytvořili [lokátor streamování](streaming-locators-concept.md) a vytvořili adresy URL streamování odpovídající podporovaným protokolům, které pak mohou být předány zařízením nebo aplikacím na základě jejich schopností.

Následující diagram znázorňuje pracovní postup pro kódování na vyžádání s dynamickým balením.

![Pracovní postup pro kódování na vyžádání s dynamickým balením](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Toto téma poskytuje pokyny, jak kódovat obsah pomocí služby Media Services v3.

## <a name="transforms-and-jobs"></a>Transformace a úlohy

Chcete-li kódovat pomocí služby Media Services v3, je třeba vytvořit [transformaci](https://docs.microsoft.com/rest/api/media/transforms) a [úlohu](https://docs.microsoft.com/rest/api/media/jobs). Transformace definuje recept pro nastavení kódování a výstupy; úloha je instancí třídy recept. Další informace najdete v tématu [Transformace a úlohy](transforms-jobs-concept.md).

Při kódování pomocí služby Media Services můžete pomocí přednastavení sdělit kodéru, jak mají být soubory vstupních médií zpracovány. Ve službě Media Services v3 se ke kódování souborů používá standardní kodér. Můžete například zadat rozlišení videa a/nebo požadovaný počet zvukových kanálů v kódovaném obsahu.

Můžete rychle začít s jedním z doporučených předvoleb založených na osvědčených postupech v oboru nebo můžete vytvořit vlastní přednastavení, které bude cílit na konkrétní scénář nebo požadavky na zařízení. Další informace naleznete [v tématu Kódování s vlastní transformací](customize-encoder-presets-how-to.md).

Počínaje lednem 2019, když kódování se standardním kodérem pro výrobu souborů MP4 vytváří nový soubor .mpi, je generován a přidán do výstupního datového zdroje. Tento soubor MPI je určen ke zlepšení výkonu pro [dynamické balení](dynamic-packaging-overview.md) a streamování scénáře.

> [!NOTE]
> Neměli byste upravovat nebo odebírat soubor MPI nebo převzít žádnou závislost ve vaší službě na existenci (nebo ne) takového souboru.

### <a name="creating-job-input-from-an-https-url"></a>Vytváření vstupu úlohy z adresy URL HTTPS

Když odešlete úlohy ke zpracování vašich videí, musíte médiím sdělit, kde vstupní video najít. Jednou z možností je zadat adresu URL HTTPS jako vstup úlohy. V současné době Media Services v3 nepodporuje kódování blokových přenosů přes adresy URL HTTPS.

#### <a name="examples"></a>Příklady

* [Kódování z adresy URL HTTPS pomocí rozhraní .NET](stream-files-dotnet-quickstart.md)
* [Kódování z adresy URL HTTPS pomocí REST](stream-files-tutorial-with-rest.md)
* [Kódování z adresy URL HTTPS pomocí cli](stream-files-cli-quickstart.md)
* [Kódování z adresy URL HTTPS pomocí souboru Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Vytváření vstupu úlohy z místního souboru

Vstupní video lze uložit jako prostředek mediální služby, v takovém případě vytvoříte vstupní prostředek založený na souboru (uloženémístně nebo v úložišti objektů Blob Azure).

#### <a name="examples"></a>Příklady

[Kódování místního souboru pomocí vestavěných přednastavení](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Vytváření vstupu úlohy s dílčím oříznutím

Při kódování videa můžete určit také oříznutí nebo oříznutí zdrojového souboru a vytvoření výstupu, který má pouze požadovanou část vstupního videa. Tato funkce funguje s libovolnou [transformací,](https://docs.microsoft.com/rest/api/media/transforms) která je vytvořena pomocí [přednastavení BuiltInStandardEncoder Preset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) nebo [přednastavení StandardEncoder.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Můžete určit vytvoření [úlohy](https://docs.microsoft.com/rest/api/media/jobs/create) s jedním klipem videa na vyžádání nebo živého archivu (nahraná událost). Vstup úlohy může být datový zdroj nebo adresa URL HTTPS.

> [!TIP]
> Pokud chcete streamovat podlip videa bez opětovného kódování videa, zvažte použití [předfiltrování manifestů pomocí dynamického balení](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Příklady

Podívejte se na příklady:

* [Podklip k videu pomocí rozhraní .NET](subclip-video-dotnet-howto.md)
* [Podklip videa pomocí REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Vestavěná přednastavení

Služba Media Services podporuje následující vestavěná přednastavení kódování:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) se používá k nastavení přednastavení pro kódování vstupního videa standardním kodérem.

V současné době jsou podporována následující přednastavení:

- **EncoderNamedPreset.AACGoodQualityAudio**: vytváří jeden soubor MP4 obsahující pouze stereo zvuk kódovaný rychlostí 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (doporučeno): Další informace naleznete v [tématu automatické generování žebříku datového toku](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: zveřejňuje experimentální přednastavení kódování podporující obsah. Vzhledem k tomu, jakýkoli vstupní obsah, služba se pokusí automaticky určit optimální počet vrstev a odpovídající datový tok a nastavení rozlišení pro doručení adaptivní streamování. Základní algoritmy se budou v průběhu času dále vyvíjet. Výstup bude obsahovat MP4 soubory s video a audio prokládání. Další informace naleznete [v tématu Experimentální přednastavení pro kódování podle obsahu](content-aware-encoding.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: vytváří sadu osmi souborů MP4 zarovnaných do GOP v rozsahu od 6000 kbps do 400 kbps a stereo AAC audio. Rozlišení začíná na 1080p a klesá na 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: vytváří sadu šesti souborů MP4 zarovnaných do GOP, od 3400 kbps do 400 kbps a stereo AAC audio. Rozlišení začíná na 720p a klesá na 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: vytváří sadu pěti souborů MP4 zarovnaných do GOP, od 1600 kbps do 400 kbps a stereo AAC audio. Rozlišení začíná na 480p a klesá na 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: vytvoří soubor MP4, kde je video kódováno kodekem H.264 při 6750 kbps a výšce obrazu 1080 pixelů a stereo zvuk je kódován kodekem AAC-LC při 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: vytvoří soubor MP4, kde je video kódováno kodekem H.264 rychlostí 4500 kbps a výškou obrazu 720 pixelů a stereo zvuk je kódován kodekem AAC-LC při k/min.
- **EncoderNamedPreset.H264SingleBitrateSD**: vytváří soubor MP4, kde je video kódováno kodekem H.264 při 2200 kbps a výšce obrazu 480 pixelů a stereo zvuk je kódován kodekem AAC-LC při 64 kbps.

Chcete-li zobrazit nejaktuálnější seznam přednastavení, přečtěte si informace o [přednastavených přednastaveních, která se mají použít pro kódování videí](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Informace o tom, jak se přednastavení používají, najdete v [tématu Nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) popisuje nastavení, která mají být použita při kódování vstupního videa se standardním kodérem. Toto přednastavení použijte při přizpůsobení přednastavení Transformace.

#### <a name="considerations"></a>Požadavky

Při vytváření vlastních přednastavení platí následující aspekty:

- Všechny hodnoty pro výšku a šířku obsahu AVC musí být násobkem čtyř.
- Ve službě Azure Media Services v3 jsou všechny přetáčicí přenosové rychlosti kódování v bitech za sekundu. To se liší od přednastavení s našimi v2 API, který používá kilobitů za sekundu jako jednotku. Například pokud byl určen přenosový tok v 2 jako 128 (kilobitů za sekundu), ve v3 by byla nastavena na 128000 (bitů za sekundu).

### <a name="customizing-presets"></a>Přizpůsobení přednastavení

Mediální služby plně podporují přizpůsobení všech hodnot v přednastaveních tak, aby vyhovovaly vašim specifickým potřebám a požadavkům kódování. Příklady, které ukazují, jak přizpůsobit přednastavení kodéru, naleznete v níže uvedeném seznamu:

#### <a name="examples"></a>Příklady

- [Přizpůsobení přednastavení pomocí rozhraní .NET](customize-encoder-presets-how-to.md)
- [Přizpůsobení přednastavení pomocí cli](custom-preset-cli-howto.md)
- [Přizpůsobení přednastavení pomocí REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Přednastavené schéma

Ve službě Media Services v3 jsou přednastavení entitami silného typu v samotném rozhraní API. Definici schématu pro tyto objekty naleznete ve [specifikaci open api (nebo Swagger).](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) Můžete také zobrazit přednastavené definice (například **StandardEncoderPreset)** v [rozhraní REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (nebo jiné referenční dokumentaci media services v3 SDK).

## <a name="scaling-encoding-in-v3"></a>Změna velikosti kódování ve v3

Chcete-li škálovat zpracování médií, [přečtěte si informace o měřítku pomocí vodíku .](media-reserved-units-cli-how-to.md)

## <a name="billing"></a>Fakturace

Služba Media Services neúčtuje za zrušené nebo chybované úlohy. Například úloha, která dosáhla 50% pokroku a je zrušena, se neúčtuje 50 % minut úlohy. Poplatky vám budou účtovány pouze za dokončené úlohy.

Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

* [Nahrávání, kódování a streamování pomocí mediálních služeb](stream-files-tutorial-with-api.md).
* [Kódovat z adresy URL HTTPS pomocí vestavěných přednastavení](job-input-from-http-how-to.md).
* [Zakódujte místní soubor pomocí vestavěných přednastavení](job-input-from-local-file-how-to.md).
* [Vytvořte vlastní přednastavení, které bude cílit na konkrétní scénář nebo požadavky na zařízení](customize-encoder-presets-how-to.md).
