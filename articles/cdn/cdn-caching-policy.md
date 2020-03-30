---
title: Správa zásad ukládání do mezipaměti Azure CDN ve službě Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak spravovat zásady ukládání do mezipaměti Azure CDN ve službě Azure Media Services.
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
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: dc0482fbcbb1c9d1618ec18e1f48b03f686a6573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74892571"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Správa zásad ukládání do mezipaměti Azure CDN ve službě Azure Media Services
Azure Media Services poskytuje adaptivní streamování založené na protokolu HTTP a postupné stahování. Streamování založené na protokolu HTTP je vysoce škálovatelné s výhodami ukládání do mezipaměti v proxy vrstvách a vrstvách CDN a ukládání do mezipaměti na straně klienta. Koncové body streamování poskytují obecné možnosti streamování a také konfiguraci pro hlavičky mezipaměti HTTP. Koncové body streamování nastaví http cache-control: maximální stáří a vyprší záhlaví. Další informace o hlavičkách mezipaměti HTTP můžete získat od [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Výchozí záhlaví ukládání do mezipaměti
Ve výchozím nastavení koncové body streamování používají 3denní hlavičky mezipaměti pro streamovaná data na vyžádání (skutečné fragmenty/bloky bloků médií) a manifest (playlist). Pro živé streamování používají koncové body streamování 3denní hlavičky mezipaměti pro data (skutečné fragmenty/bloky médií) a 2 sekundy záhlaví mezipaměti pro požadavky manifestu (playlistu). Když se živý program změní na on-demand (živý archiv), použijí se hlavičky mezipaměti streamování na vyžádání.

## <a name="azure-cdn-integration"></a>Integrace Azure CDN
Azure Media Services poskytuje [integrované CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) pro koncové body streamování. Záhlaví řízení mezipaměti platí stejným způsobem jako koncové body streamování na koncové body datových proudů s povolenou technologií CDN. Azure CDN používá hodnoty mezipaměti nakonfigurované datovým bodem k definování doby životnosti objektů uložených v interní mezipaměti a také používá tuto hodnotu k nastavení záhlaví mezipaměti doručení. Při použití koncových bodů datových proudů povolených technologií CDN se nedoporučuje nastavovat malé hodnoty mezipaměti. Nastavení malých hodnot snižuje výkon a snižuje výhody CDN. Není povoleno nastavit záhlaví mezipaměti menší než 600 sekund pro koncové body streamování povoleno CDN.

> [!IMPORTANT]
>Azure Media Services má kompletní integraci s Azure CDN. Jediným kliknutím můžete integrovat všechny dostupné poskytovatele Azure CDN do vašeho koncového bodu streamování, včetně standardních a prémiových produktů. Další informace naleznete v tomto [oznámení](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Poplatky za data z koncového bodu streamování na CDN se deaktivují jenom v případě, že je cdn povolena přes rozhraní API koncového bodu streamování nebo pomocí části koncového bodu streamování na webu Azure Portal. Ruční integrace nebo přímé vytvoření koncového bodu CDN pomocí cdn api nebo portálu části nezakáže poplatky za data.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurace záhlaví mezipaměti pomocí Služby Azure Media Services
K konfiguraci hodnot hlaviček mezipaměti můžete použít azure portál nebo rozhraní API Azure Media Services.

1. Pokud chcete nakonfigurovat záhlaví mezipaměti pomocí portálu Azure Portal, přečtěte si část [Správa koncových bodů streamování](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) Konfigurace koncového bodu streamování.
2. Rozhraní REST API Azure Media Services, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [Vlastnosti StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Pořadí priorit konfigurace mezipaměti
1. Azure Media Services nakonfigurované hodnoty mezipaměti přepíše výchozí hodnotu.
2. Pokud neexistuje žádná ruční konfigurace, platí výchozí hodnoty.
3. Ve výchozím nastavení 2 sekundy cache záhlaví platí pro živé streamování manifest (playlist) bez ohledu na Azure Media nebo Azure Storage konfigurace a přepsání této hodnoty není k dispozici.

