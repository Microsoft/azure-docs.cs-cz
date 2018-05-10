---
title: Přehled dynamického balení Azure Media Services | Microsoft Docs
description: Téma nabízí a přehled dynamického balení.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 8f05015da1f66331413086c0e27c25cd5da75f5c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="dynamic-packaging"></a>Dynamické balení
## <a name="overview"></a>Přehled
Ochrana obsahu formáty do různých technologií, klienta nebo Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formáty souborů, formátů streamování médií (například iOS, XBOX, Silverlight, Windows 8). Tito klienti pochopit různé protokoly, například vyžaduje formátu V4 HTTP Live Streaming (HLS), iOS a Silverlight a Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (více přenosovými rychlostmi) MP4 soubory (ISO základní médium 14496-12) nebo sadu technologie Smooth Streaming souborů s adaptivní přenosovou rychlostí, která má sloužit ke klientům, kteří pochopit MPEG DASH, HLS nebo technologie Smooth Streaming, byste měli vzít výhod média Dynamické balení služby.

Při dynamickém balení, stačí je vytvoření asset, který obsahuje sadu souborů MP4 nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí. Pak na základě formátu určeného v manifestu nebo fragmentu požadavek streamingu na vyžádání serveru zajistí datový proud obdrželi v protokolu, kterou jste vybrali. Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Následující diagram znázorňuje tradiční kódování a statické balení pracovního postupu.

![Statické kódování](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Následující diagram znázorňuje dynamické balení pracovního postupu.

![Dynamické kódování](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>Běžný scénář
1. Nahrajte vstupní soubor (nazývané soubor mezzanine). Například H.264, MP4 nebo WMV (seznam podporovaných formátů naleznete v části [formáty nepodporuje Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte asset, který obsahuje tak, že vytvoříte Lokátor na vyžádání sady souborů MP4 s adaptivní přenosovou.
4. Vytvoření datových proudů adres URL pro přístup k a Streamovat obsah.

## <a name="preparing-assets-for-dynamic-streaming"></a>Příprava prostředky pro dynamické streamování
Příprava asset pro dynamické streamování máte dvě možnosti:

1. [Nahrát soubor hlavní](media-services-dotnet-upload-files.md).
2. [Vytvoření sady H.264 MP4 s adaptivní přenosovou rychlostí pomocí kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Streamovat obsah](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Formáty, které nepodporuje dynamické balení
Dynamické balení nejsou podporovány následující formáty souborů zdroje.

* Soubory mp4 digitální Dolby.
* Digitální soubory technologie smooth Dolby.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

