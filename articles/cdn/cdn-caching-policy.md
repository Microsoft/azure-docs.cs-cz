---
title: Spravovat zásady ve službě Azure Media Services ukládání do mezipaměti Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak spravovat Azure CDN ve službě Azure Media Services zásady ukládání do mezipaměti.
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
ms.openlocfilehash: ac94370b1c6a8f48ad55f0e277d93cd2f8388cb1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242598"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Správa Azure CDN ve službě Azure Media Services zásady ukládání do mezipaměti
Azure Media Services nabízí adaptivní streamování a progresivní stahování založený na HTTP. Streamování na základě protokolu HTTP je vysoce škálovatelná výhody ukládání do mezipaměti v proxy serveru a vrstvy CDN, stejně jako ukládání do mezipaměti na straně klienta. Koncové body streamování poskytuje obecné funkce streamování a také konfigurace mezipaměti hlaviček protokolu HTTP. Koncové body streamování nastaví HTTP Cache-Control: maximální stáří a záhlaví Expires. Můžete získat další informace o mezipaměti hlavičky protokolu HTTP z [adrese W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Výchozí záhlaví ukládání do mezipaměti
Ve výchozím nastavení se vztahují 3denní hlavičky mezipaměti pro streamovaná data na vyžádání (fragmentů samotný mediální bloků dat) a manifest(playlist) – koncové body streamování. Pro živé streamování koncové body streamování použít 3denní hlavičky mezipaměti pro data (fragmentů samotný mediální bloků dat) a záhlaví manifest(playlist) žádostí do mezipaměti 2 sekundy. Když živou program se změní na vyžádání (živý archív), pak použijte hlavičky mezipaměti streamování na vyžádání.

## <a name="azure-cdn-integration"></a>Integrace s Azure CDN
Azure Media Services nabízí [integrované CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) pro streamování koncové body. Hlavičky cache-control platí stejným způsobem jako koncové body streamování do CDN povolené koncové body streamování. Azure CDN používá koncový bod streamování nakonfigurované hodnoty mezipaměti definovat dobu životnosti objektů interně uložená v mezipaměti a také používá tuto hodnotu nastavit hlavičky mezipaměti doručování. Při používání CDN povolené koncové body streamování se nedoporučuje k nastavení hodnot malé mezipaměti. Malé hodnoty nastavení snížit výkon a snížit výhodou CDN. Chcete-li nastavit hlavičky mezipaměti menší než 600 sekund pro CDN povolené koncové body streamování není povoleno.

> [!IMPORTANT]
>Azure Media Services je kompletní integrovaná se sadou Azure CDN. Jediným kliknutím můžete integrovat všechny dostupné zprostředkovatele Azure CDN na váš koncový bod streamování, včetně produktů úrovně standard a premium. Další informace najdete v tomto [oznámení](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Poplatky za data z datových proudů koncový bod CDN získává pouze zakázaná, pokud je CDN povolená přes koncový bod rozhraní API pro streamování nebo pomocí webu Azure portal streamování oddíl koncového bodu. Ruční integraci nebo přímé vytváření koncového bodu CDN pomocí rozhraní API CDN nebo portálu části nemá zakázat poplatky za data.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurace hlaviček mezipaměti pomocí služby Azure Media Services
Azure portal nebo rozhraní API služeb Azure Media Services můžete použít ke konfiguraci hodnoty hlavičky mezipaměti.

1. Konfigurace hlaviček mezipaměti pomocí webu Azure portal, najdete v tématu [jak spravovat koncové body streamování](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) oddíl konfigurace koncového bodu streamování.
2. Rozhraní REST API služby Azure Media Services [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK [StreamingEndpointCacheControl vlastnosti](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Pořadí priority konfigurace mezipaměti
1. Azure Media Services nakonfigurovaná hodnota mezipaměti přepíše výchozí hodnotu.
2. Pokud není žádná ruční konfigurace, použije se výchozí hodnoty.
3. Pomocí výchozí 2 sekundy mezipaměti hlaviček platí pro živé streamování manifest(playlist) bez ohledu na to konfigurace mediální služby Azure nebo Azure Storage a přepsání této hodnoty není k dispozici.

