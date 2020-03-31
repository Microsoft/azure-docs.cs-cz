---
title: Migrace z Azure Media Services v2 na v3 | Dokumenty společnosti Microsoft
description: Tento článek popisuje změny, které byly zavedeny ve službě Azure Media Services v3 a ukazuje rozdíly mezi dvěma verzemi. Článek také poskytuje pokyny pro migraci pro přechod z Media Services v2 na v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472063"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Pokyny pro migraci pro přechod z Media Services v2 na v3

>Získejte upozornění, kdy se má tato stránka znovu aktualizovat zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` do čtečky informačních kanálů RSS.

Tento článek obsahuje pokyny k migraci z Media Services v2 na v3.

Pokud máte video služby vyvinuté dnes na starší [media services v2 rozhraní API](../previous/media-services-overview.md), měli byste si přečíst následující pokyny a aspekty před migrací na rozhraní API v3. Existuje mnoho výhod a nových funkcí v rozhraní API verze 3, které zlepšují prostředí pro vývojáře a možnosti mediálních služeb. Však jak je uvedeno v části [známé problémy](#known-issues) tohoto článku, existují také některá omezení z důvodu změn mezi verzemi rozhraní API. Tato stránka bude zachována, protože tým mediálních služeb provádí další vylepšení rozhraní API verze 3 a řeší mezery mezi verzemi. 

## <a name="prerequisites"></a>Požadavky

* Recenze [Media Services v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Výhody mediálních služeb v3
  
### <a name="api-is-more-approachable"></a>API je přístupnější

*  V3 používá prostor Unified API, který zpřístupňuje funkce pro správu i provoz založené na Azure Resource Manageru. Šablony Azure Resource Manageru se můžou použít k vytváření a nasazování transformací, koncových bodů streamování, živých událostí a dalších.
* [Dokument OpenAPI Specification (dříve nazývaný Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Zpřístupní schéma pro všechny součásti služby, včetně kódování založené na souboru.
* Sady SDK jsou k dispozici pro [sady .NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref)a Ruby.
* [Integrace Azure CLI](https://aka.ms/ams-v3-cli-ref) pro jednoduchou podporu skriptování.

### <a name="new-features"></a>Nové funkce

* Pro zpracování úlohy založené na souborech můžete jako vstup použít adresu URL HTTP(S).<br/>Nemusíte mít obsah, který už máte uložený v Azure, ani nemusíte vytvářet datové zdroje.
* Zavádí koncept [transformace](transforms-jobs-concept.md) pro zpracování úloh y založené na souborech. Transformace lze použít k vytvoření opakovaně použitelné konfigurace, k vytvoření šablony Azure Resource Manager a izolovat nastavení zpracování mezi více zákazníků nebo klientů.
* Datový zdroj může mít více [lokátorů streamování,](streaming-locators-concept.md) z nichž každý má jiné [nastavení dynamického balení](dynamic-packaging-overview.md) a dynamického šifrování.
* [Ochrana obsahu](content-key-policy-concept.md) podporuje funkce s více klíči.
* Při použití služby Media Services pro překódování jednoho kanálu dat do výstupního datového proudu, který má více datových toků, můžete streamovat živé události, které jsou až 24 hodin dlouhé.
* Nová podpora živého streamování s nízkou latencí v živých událostech. Další informace naleznete v [tématu latence](live-event-latency.md).
* Náhled živých událostí podporuje [dynamické balení](dynamic-packaging-overview.md) a dynamické šifrování. To umožňuje ochranu obsahu na náhledu, stejně jako DASH a HLS balení.
* Živý výstup je jednodušší než entita Program v polích V2 API. 
* Vylepšená podpora RTMP (zvýšená stabilita a větší podpora zdrojového kodéru).
* Bezpečné ingestování RTMPS.<br/>Když vytvoříte živou událost, získáte 4 adresy URL ingestování. 4 ingestující adresy URL jsou téměř identické, mají stejný token streamování (AppId), pouze číslo portu část se liší. Dvě adresy URL jsou primární a zálohy pro RTMPS.   
* Máte řízení přístupu na základě rolí (RBAC) přes vaše entity. 

## <a name="known-issues"></a>Známé problémy

*  V současné době můžete na [webu Azure Portal:](https://portal.azure.com/)

    * správa mediálních služeb v3 [živé události](live-events-outputs-concept.md), 
    * zobrazení (nespravuje) v3 [Aktiva](assets-concept.md), 
    * [získat informace o přístupu k api](access-api-portal.md). 

    Pro všechny ostatní úlohy správy (například [Transformace a úlohy](transforms-jobs-concept.md) a [ochrana obsahu](content-protection-overview.md)) použijte rozhraní [REST API](https://docs.microsoft.com/rest/api/media/), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).
* Chcete-li řídit souběžnost a výkon úloh, zejména ty, které zahrnují analýzu videa nebo zvuku, je třeba ve svém účtu zřídit rezervované jednotky médií.You need to provision Media Reserved Units (MRU) in your account to control the concurrency and performance of your Jobs, especially ty, které zahrnují video nebo audio analýzu. Další informace najdete v článku o [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). Mru můžete spravovat pomocí [příkazového příkazu CLI 2.0 pro media services v3](media-reserved-units-cli-how-to.md), pomocí [portálu Azure](../previous/media-services-portal-scale-media-processing.md)nebo pomocí [v2 API](../previous/media-services-dotnet-encoding-units.md). Je třeba zřídit MRU, ať už používáte media services v2 nebo v3 API.
* Entity media services vytvořené pomocí rozhraní API v3 nelze spravovat pomocí rozhraní API v2.  
* Ne všechny entity v rozhraní API V2 se automaticky zobrazí v rozhraní API V3.  Následují příklady entit ve dvou verzích, které jsou nekompatibilní:  
    * Úlohy a úkoly vytvořené ve v2 se nezobrazují ve v3, protože nejsou přidruženy k transformaci. Doporučujeme přepnout na v3 transformace a úlohy. Během přechodu bude relativně krátká doba nutnosti sledovat pracovní místa v2 za letu.
    * Kanály a programy vytvořené pomocí v2 (které jsou mapovány na živé události a živé výstupy ve v3) nelze nadále spravovat pomocí v3. Doporučujeme přepnout na živé události v3 a živé výstupy na pohodlné zastávce kanálu.<br/>V současné době nelze migrovat neustále spuštěné kanály.  

> [!NOTE]
> Tato stránka bude zachována, protože tým mediálních služeb provádí další vylepšení rozhraní API verze 3 a řeší mezery mezi verzemi.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

[Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - .NET](stream-files-dotnet-quickstart.md)
