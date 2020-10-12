---
title: Správa zásad ukládání do mezipaměti Azure CDN v Azure Media Services | Microsoft Docs
description: Tento článek vysvětluje, jak spravovat zásady ukládání do mezipaměti Azure CDN v Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 6beaee98e78e79c48270801f5696e4e487b0a2c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84883709"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Správa zásad ukládání do mezipaměti Azure CDN v Azure Media Services
Azure Media Services poskytuje adaptivní streamování a progresivní stahování založené na protokolu HTTP. Streamování založené na protokolu HTTP je vysoce škálovatelné s výhodami ukládání do mezipaměti ve vrstvách proxy a CDN i do ukládání do mezipaměti na straně klienta. Koncové body streamování poskytuje obecné možnosti streamování a také konfiguraci pro hlavičky mezipaměti protokolu HTTP. Koncové body streamování nastavuje mezipaměť HTTP – řízení cache: maximální stáří a hlavičky vypršení platnosti. Další informace o hlavičkách mezipaměti HTTP najdete v [w3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Výchozí hlavičky ukládání do mezipaměti
Ve výchozím nastavení se koncové body streamování použijí 3 den v mezipaměti pro streamovaná data na vyžádání (skutečné fragmenty médií/bloky dat) a MANIFEST (seznam skladeb). V případě živého streamování se koncové body streamování použijí 3 den mezipaměti pro data (skutečné fragmenty médií/bloky dat) a 2 sekundy v hlavičce mezipaměti pro žádosti manifest (Playlist). Když se živý program zapne na vyžádání (Live Archive), použijí se hlavičky mezipaměti streamování na vyžádání.

## <a name="azure-cdn-integration"></a>Integrace Azure CDN
Azure Media Services poskytuje [integrované CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) pro streamování – koncové body. Hlavičky Cache-Control se použijí stejným způsobem jako koncové body streamování pro koncové body streamování s povoleným CDN. Azure CDN používá nakonfigurované hodnoty mezipaměti pro streamování dat k definování životnosti objektů interně uložených v mezipaměti a používá tuto hodnotu k nastavení hlaviček mezipaměti pro doručování. Při použití koncových bodů streamování s povoleným CDN se nedoporučuje nastavit malé hodnoty mezipaměti. Nastavením malých hodnot se sníží výkon a sníží se výhoda CDN. U koncových bodů streamování povolených přes CDN není povoleno nastavení hlaviček mezipaměti kratších než 600 sekund.

> [!IMPORTANT]
>Azure Media Services dokončí integraci s Azure CDN. Jediným kliknutím můžete integrovat všechny dostupné poskytovatele Azure CDN do koncového bodu streamování, včetně produktů Standard a Premium. Další informace najdete v tomto [oznámení](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Poplatky za datové proudy z koncového bodu streamování do CDN se neaktivují jenom v případě, že je služba CDN povolená přes rozhraní API koncového bodu streamování nebo Azure Portal pomocí Ruční integrace nebo přímé vytvoření koncového bodu CDN pomocí rozhraní CDN API nebo portálu nezakáže poplatky za data.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurace hlaviček mezipaměti pomocí Azure Media Services
K nakonfigurování hodnot hlaviček mezipaměti můžete použít rozhraní API Azure Portal nebo Azure Media Services.

1. Pokud chcete konfigurovat hlavičky mezipaměti pomocí Azure Portal, přečtěte si téma [Správa koncových bodů streamování](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) s konfigurací koncového bodu streamování.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [vlastnosti StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Pořadí priority konfigurace mezipaměti
1. Azure Media Services konfigurovaná hodnota mezipaměti přepisuje výchozí hodnotu.
2. Pokud není k dispozici žádná ruční konfigurace, použijí se výchozí hodnoty.
3. Ve výchozím nastavení se do 2 sekund použijí hlavičky mezipaměti pro živý streamování (Playlist) bez ohledu na média Azure nebo konfiguraci Azure Storage a přepsání této hodnoty není k dispozici.

