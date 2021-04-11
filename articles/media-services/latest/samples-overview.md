---
title: Ukázky Media Services v3
description: Tento článek obsahuje seznam všech ukázek dostupných pro Media Services V3 uspořádané podle metody a sady SDK.  Ukázky zahrnují rozhraní .NET, Node.JS, Python a Java a také REST s nástrojem pro odesílání.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1d827d734c434204ff6b7ec60d27e507ae626abd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227683"
---
# <a name="media-services-v3-samples"></a>Ukázky Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek obsahuje seznam všech ukázek, které jsou k dispozici pro Media Services uspořádané podle metody a sady SDK.  Ukázky zahrnují rozhraní .NET, Node.JS, Python a Java a také REST s nástrojem pro odesílání.

## <a name="rest-postman-collection"></a>Kolekce po REST

Ukázky pro [REST](https://github.com/Azure-Samples/media-services-v3-rest-postman) obsahují předzálohovací prostředí a kolekci pro import do tohoto klienta.

## <a name="samples-by-sdk"></a>Ukázky podle sady SDK

Najdete popis a odkazy na ukázky, které můžete hledat na jednotlivých kartách.

## <a name="net"></a>[.NET](#tab/net/)

| Složka | Description |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Postup odeslání úlohy pomocí předdefinované předvolby a vstupu adresy URL HTTP, publikování výstupní assetu pro streamování a stažení výsledků pro ověření.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Odeslání úlohy pomocí vlastní předvolby pro kódování H. 264 a zadání adresy URL HTTP, publikování výstupního prostředku pro streamování a stažení výsledků pro ověření.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Odeslání úlohy pomocí vlastního přednastavení kódování HEVC a vstupu adresy URL HTTP, publikování výstupní assetu pro streamování a stažení výsledků pro ověření.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Jak odeslat úlohu pomocí JobInputSequence dohromady 2 nebo více prostředků, které mohou být oříznuty počátečním nebo koncovým časem. Výsledný kódovaný soubor je jedno video se všemi prostředky, které se společně spolupracovaly.  Ukázka také publikuje výstupní Asset pro streamování a výsledky stahování pro ověření.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Odeslání úlohy pomocí vlastního přednastavení s pohyblivým symbolem miniatury a zadáním adresy URL HTTP, publikováním výstupního assetu pro streamování a stažením výsledků pro ověření.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Jak vytvořit Livestream s úplným archivem až 25 hodin a filtrem assetu s oknem DVR 5 minut. Jak použít filtr k vytvoření lokátoru pro streamování.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Jak vytvořit transformaci videa, nahrát videosoubor do vstupního assetu, odeslat úlohu s transformací a stáhnout výsledky pro ověření.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Jak vytvořit transformaci zvukového analyzátoru, nahrát mediální soubor do vstupního assetu, odeslat úlohu s transformací a stáhnout výsledky pro ověření.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Jak vytvořit transformaci s integrovaným přednastaveným AdaptiveStreaming, odeslat úlohu, vytvořit ContentKeyPolicy pomocí tajného klíče, přidružit ContentKeyPolicy k StreamingLocator, získat token a vytisknout adresu URL pro přehrávání v Azure Media Player. Když přehrávač požaduje datový proud, Media Services používá zadaný klíč k dynamickému šifrování vašeho obsahu pomocí AES-128 a Azure Media Player používá token k dešifrování.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Jak vytvořit transformaci s integrovaným přednastaveným AdaptiveStreaming, odeslat úlohu, vytvořit ContentKeyPolicy s konfigurací Widevine pomocí tajného klíče, přidružit ContentKeyPolicy k StreamingLocator, získat token a vytisknout adresu URL pro přehrávání v přehrávači Widevine. Když si uživatel vyžádá obsah chráněný Widevine, aplikace Player si vyžádá licenci od služby Media Services License Service. Pokud je aplikace přehrávače oprávněná, licenční služba Media Services vydá přehrávači licenci. Licence Widevine obsahuje dešifrovací klíč, který může klientský přehrávač použít k dešifrování a streamování obsahu.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Jak vytvořit transformaci s integrovaným přednastaveným AdaptiveStreaming, odeslat úlohu, vytvořit ContentKeyPolicy s konfigurací PlayReady pomocí tajného klíče, přidružit ContentKeyPolicy k StreamingLocator, získat token a vytisknout adresu URL pro přehrávání v Azure Media Player. Když si uživatel vyžádá obsah chráněný pomocí PlayReady, aplikace přehrávače si vyžádá licenci od služby Media Services License Service. Pokud je aplikace přehrávače oprávněná, licenční služba Media Services vydá přehrávači licenci. Licence PlayReady obsahuje dešifrovací klíč, který může klientský přehrávač použít k dešifrování a streamování obsahu.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Jak dynamicky Šifrovat obsah pomocí PlayReady a Widevine DRM a přehrát obsah bez žádosti o licenci od licenční služby. Ukazuje, jak vytvořit transformaci s integrovaným přednastaveným AdaptiveStreaming, odeslat úlohu, vytvořit ContentKeyPolicy s omezením Open a s trvalou konfigurací PlayReady/Widevine, přidružit ContentKeyPolicy k StreamingLocator a vytisknout adresu URL pro přehrávání.|
| [Streamování/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Jak vytvořit transformaci s integrovaným předplatným AdaptiveStreaming, odeslat úlohu, vytvořit filtr assetu a filtr účtu, přidružit filtry ke lokátorům streamování a tisknout adresy URL pro přehrávání.|
| [Streamování/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Jak vytvořit transformaci s integrovaným předplatným AdaptiveStreaming, odeslat úlohu, publikovat výstupní Asset pro HLS a PŘERUŠOVANé streamování.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Doprovodné materiály a osvědčené postupy pro produkční systém s využitím kódování nebo analýz na vyžádání. Čtenáři by měli začít s vysokou dostupností v doprovodném článku [s Media Services a vod](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept). Pro ukázku [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) je k dispozici samostatný soubor řešení. |

## <a name="nodejs"></a>[Node.js](#tab/node/)

|Složka|Description|
|---|---|
|[HelloWorld – ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Jak připojit a vypsat prostředky |
|[Živě](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Jak provést základní živé streamování. **Upozornění**, nezapomeňte zkontrolovat, jestli jsou všechny prostředky vyčištěné a už se neúčtují na portálu při použití Live.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Jak nahrát místní soubor nebo kódování ze zdrojové adresy URL, jak použít sadu SDK pro úložiště ke stažení obsahu a jak streamovat do přehrávače. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Jak kódovat a streamovat pomocí Widevine a PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Jak používat přednastavení video a audio Analyzer k vygenerování metadat a přehledů z videa nebo zvukového souboru. |

## <a name="python"></a>[Python](#tab/python)

V současné době existuje jedna ukázka Pythonu, [základní kódování pomocí Pythonu](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Složka|Description|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Jak analyzovat zvuk v mediálním souboru. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Jak dynamicky Šifrovat obsah pomocí AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Jak dynamicky Šifrovat obsah pomocí technologie PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Jak dynamicky Šifrovat obsah pomocí Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Postup dynamického šifrování obsahu pomocí FairPlay DRM a přehrání obsahu bez žádosti o licenci od licenční služby.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Jak dynamicky Šifrovat obsah pomocí PlayReady a Widevine DRM a přehrát obsah bez žádosti o licenci od licenční služby.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Filtrování obsahu pomocí filtrů Asset a account.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Postup dynamického balení VOD obsahu do HLS/POMLČKy pro streamování.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Jak vytvořit živou událost s úplnou archivací až 25 hodin a filtrem assetu s oknem DVR 5 minut a vytvořit Lokátor pro streamování a použít filtr.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Jak vytvořit živou událost s úplnou archivací až 25 hodin a filtrem assetu s oknem DVR 5 minut a vytvořit Lokátor pro streamování a použít filtr.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Jak vytvořit vlastní transformaci kódování pomocí nastavení StandardEncoderPreset|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Postup odeslání úlohy pomocí předdefinované předvolby a vstupu adresy URL HTTP, publikování výstupní assetu pro streamování a stažení výsledků pro ověření.|

---
