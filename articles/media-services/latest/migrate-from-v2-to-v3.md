---
title: Migrace z Azure Media Services V2 na V3 | Microsoft Docs
description: Tento článek popisuje změny, které byly představeny v Azure Media Services V3, a ukazuje rozdíly mezi dvěma verzemi. Článek také poskytuje pokyny k migraci pro přesun z Media Services V2 na v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0cdf4699f5dc00087845ee6ca0d24ad6493c320b
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426868"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Pokyny k migraci pro přesun z Media Services V2 na V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` do čtečky kanálů RSS.

Tento článek poskytuje pokyny k migraci z Media Services V2 na v3.

Pokud máte k dispozici službu video Service na [starší verzi rozhraní api Media Services V2](../previous/media-services-overview.md), měli byste před migrací na rozhraní API V3 zkontrolovat následující pokyny a důležité informace. Rozhraní V3 API nabízí spoustu výhod a nových funkcí, které zlepšují vývojové prostředí a možnosti Media Services. Nicméně jak je uvedeno v části [známé problémy](#known-issues) v tomto článku, existují také určitá omezení v důsledku změn mezi verzemi rozhraní API. Tato stránka bude zachována, protože Media Services tým zajišťuje pokračování v vylepšeních rozhraní API v3 a řeší mezery mezi verzemi. 

## <a name="prerequisites"></a>Požadavky

* Kontrola [Media Services V2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Výhody Media Services V3
  
### <a name="api-is-more-approachable"></a>Rozhraní API je více možností přístupu

*  V3 používá prostor Unified API, který zpřístupňuje funkce pro správu i provoz založené na Azure Resource Manageru. Šablony Azure Resource Manager lze použít k vytváření a nasazování transformací, koncových bodů streamování, živých událostí a dalších.
* [Openapi specifikace (dříve označované jako Swagger)](https://aka.ms/ams-v3-rest-sdk) dokument.
    Zpřístupňuje schéma pro všechny součásti služby, včetně kódování založeného na souborech.
* Sady SDK dostupné pro [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core [ ,Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management), [Java](/java/api/overview/azure/mediaservices/management), [Přejít](https://aka.ms/ams-v3-go-ref)a Ruby.
* Integrace [Azure CLI](/cli/azure/ams) pro jednoduchou podporu skriptování

### <a name="new-features"></a>Nové funkce

* Pro zpracování úloh založených na souborech můžete jako vstup použít adresu URL HTTP (S).<br/>Nemusíte mít již uložený obsah v Azure, ani nemusíte vytvářet prostředky.
* Zavádí koncepci [transformací](transforms-jobs-concept.md) pro zpracování úloh založených na souborech. Transformaci lze použít k vytvoření opakovaně použitelných konfigurací, k vytvoření Azure Resource Manager šablon a k izolaci nastavení zpracování mezi několika zákazníky nebo klienty.
* Asset může mít několik [lokátorů streamování](streaming-locators-concept.md) s různými nastaveními [dynamického balení](dynamic-packaging-overview.md) a dynamického šifrování.
* [Content Protection](content-key-policy-concept.md) podporuje více klíčových funkcí.
* Při použití Media Services pro překódování informačního kanálu s jedním přenosovou rychlostí do výstupního datového proudu s více přenosovými rychlostmi můžete streamovat živé události, které jsou až 24 hodin dlouhé.
* Nová podpora živého streamování s nízkou latencí pro živé události Další informace najdete v tématu [latence](live-event-latency.md).
* Live Event Preview podporuje [dynamické balení](dynamic-packaging-overview.md) a dynamické šifrování. To umožňuje ochranu obsahu ve verzi Preview i SPOJOVNÍK a HLS balení.
* Živý výstup je jednodušší použít než entita programu v rozhraních API v2. 
* Vylepšená podpora RTMP (zvýšená stabilita a lepší podpora zdrojového kodéru).
* Zabezpečení pro ingestování RTMP<br/>Při vytváření živé události získáte čtyři adresy URL pro příjem. 4 adresy URL pro přijímání jsou skoro stejné, mají stejný token streamování (AppId), ale liší se jenom část číslo portu. Dvě z těchto adres URL jsou primární a zálohují pro RTMP.   
* Máte k dispozici řízení přístupu na základě role Azure (Azure RBAC) prostřednictvím svých entit. 

## <a name="known-issues"></a>Známé problémy

*  V současné době můžete použít [Azure Portal](https://portal.azure.com/) k těmto akcím:

    * Správa událostí Media Services V3 [Live](live-events-outputs-concept.md) 
    * Zobrazit (Nespravovat) 3 [prostředky](assets-concept.md)V3 
    * [Získejte informace o přístupu k rozhraním API](./access-api-howto.md). 

    Pro všechny ostatní úlohy správy (například [transformace a úlohy](transforms-jobs-concept.md) a [Ochrana obsahu](content-protection-overview.md)) použijte [REST API](/rest/api/media/), [CLI](/cli/azure/ams)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).
* Ve svém účtu musíte zřídit rezervované jednotky médií (MRUs), aby bylo možné řídit souběžnost a výkon vašich úloh, zejména ty, které zahrnují analýzu videa nebo zvuku. Další informace najdete v článku o [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). MRUs můžete spravovat pomocí [CLI 2,0 pro Media Services V3](media-reserved-units-cli-how-to.md), pomocí [Azure Portal](../previous/media-services-portal-scale-media-processing.md)nebo pomocí [rozhraní API v2](../previous/media-services-dotnet-encoding-units.md). Musíte zřídit MRUs, ať už používáte rozhraní API Media Services v2 nebo V3.
* Entity Media Services vytvořené s rozhraním API V3 se nedají spravovat pomocí rozhraní API v2.  
* Ne všechny entity v rozhraní v2 API se automaticky zobrazují v rozhraní V3 API.  Následují příklady entit v těchto dvou verzích, které jsou nekompatibilní:  
    * Úlohy a úlohy vytvořené v v2 se v v3 nezobrazují, protože nejsou přidružené k transformaci. Doporučení je přepnuto na transformace v3 a úlohy. Během přepínání bude existovat poměrně krátká doba nutná ke sledování úloh inlety v2.
    * Kanály a programy vytvořené pomocí v2 (které jsou namapované na živé události a živé výstupy ve verzi V3) nemůžou dál spravovat pomocí v3. Doporučení je přepnout na hodnotu v3 živé události a živé výstupy na pohodlném zastavení kanálu.<br/>V současné době nemůžete migrovat nepřetržitě běžící kanály.  

> [!NOTE]
> Tato stránka bude zachována, protože Media Services tým zajišťuje pokračování v vylepšeních rozhraní API v3 a řeší mezery mezi verzemi.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Kurz: kódování vzdáleného souboru na základě adresy URL a streamu pro video – .NET](stream-files-dotnet-quickstart.md)