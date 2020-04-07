---
title: Porovnání funkcí produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Seznamte se s podporovanými funkcemi v jednotlivých produktech Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 540dd51abf8b832194c3814c8198cb72cc60d348
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745448"
---
# <a name="compare-azure-cdn-product-features"></a>Porovnání funkcí produktu Azure CDN

Azure Content Delivery Network (CDN) zahrnuje čtyři produkty: **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Akamai**, **Azure CDN Standard od společnosti Verizon** a **Azure CDN Premium od společnosti Verizon**. Informace o migraci profilu **Azure CDN Standard od společnosti Verizon** na profil **Azure CDN Premium od společnosti Verizon** najdete v článku [Migrace profilu Azure CDN z úrovně Standard Verizon na úroveň Premium Verizon](cdn-migrate.md). Všimněte si, že zatímco existuje cesta upgradu ze standardu Verizon na Premium Verizon, v tuto chvíli neexistuje žádný mechanismus převodu mezi jinými produkty.

Následující tabulka obsahuje porovnání funkcí, které jsou dostupné v jednotlivých produktech.

| **Funkce výkonu a optimalizace** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Dynamická akcelerace webu](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Nabízeno prostřednictvím [služby Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamická akcelerace webu – adaptivní komprese obrazu](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamická akcelerace webu – předběžné načtení objektu](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Obecná optimalizace doručování webových stránek](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Vyberte tento typ optimalizace, pokud je průměrná velikost souboru menší než 10 MB.  | **&#x2713;** |  **&#x2713;** |
| [Optimalizace streamování videa](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | prostřednictvím obecného doručování webových stránek | **&#x2713;**  | prostřednictvím obecného doručování webových stránek |  prostřednictvím obecného doručování webových stránek |
| [Optimalizace velkých souborů](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | prostřednictvím obecného doručování webových stránek | **&#x2713;**, Vyberte tento typ optimalizace, pokud je průměrná velikost souboru větší než 10 MB.   | prostřednictvím obecného doručování webových stránek |  prostřednictvím obecného doručování webových stránek |
| Změna typu optimalizace | |**&#x2713;** | | |
| Port původu |Všechny porty TCP |[Povolené porty původu](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Všechny porty TCP |Všechny porty TCP |
| [Globální vyrovnávání zatížení serveru (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rychlé vyprázdnění](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, vyčistit všechny a vyprázdnění zástupných symbolů nejsou podporovány Azure CDN z Akamai v současné době |**&#x2713;** |**&#x2713;** |
| [Předběžné načítání prostředku](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Nastavení mezipaměti nebo hlaviček (pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md))  |**&#x2713;** pomocí [modulu standardních pravidel](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Přizpůsobitelný modul pro doručování obsahu založený na pravidlech |**&#x2713;** pomocí [modulu standardních pravidel](cdn-standard-rules-engine.md)  | | |**&#x2713;** pomocí [modulu pravidel](cdn-rules-engine.md) |
| Nastavení mezipaměti/záhlaví  |**&#x2713;** pomocí [modulu standardních pravidel](cdn-standard-rules-engine.md) | | |**&#x2713;** pomocí [motoru premium pravidel](cdn-rules-engine.md) |
| Přesměrování/přepsání adresy URL |**&#x2713;** pomocí [modulu standardních pravidel](cdn-standard-rules-engine.md)  | | |**&#x2713;** pomocí [motoru premium pravidel](cdn-rules-engine.md) |
| Pravidla pro mobilní zařízení  |**&#x2713;** pomocí [modulu standardních pravidel](cdn-standard-rules-engine.md) | | |**&#x2713;** pomocí [motoru premium pravidel](cdn-rules-engine.md) |
| [Ukládání řetězce dotazu do mezipaměti](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Duální sada protokolů IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podpora HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpečení** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Podpora HTTPS s koncovými body CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS pro vlastní doménu](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, vyžaduje přímé CNAME povolit |**&#x2713;** |**&#x2713;** |
| [Podpora vlastních názvů domén](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geografické filtrování](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ověření tokenu](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrana DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Používání vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Podporované verze TLS | TLS 1.2, TLS 1.0/1.1 - [Konfigurovatelné](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analýza a reportování** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| [Diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Základní sestavy od Verizonu](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Vlastní sestavy od Verizonu](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiky v reálném čase](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Výkon hraničních uzlů](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Výstrahy v reálném čase](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Snadné používání** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Snadná integrace se službami Azure, jako jsou [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Správa pomocí [rozhraní REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)nebo [PowerShellu](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Typy kompresních MIME](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Pouze výchozí |Konfigurovatelné |Konfigurovatelné  |Konfigurovatelné  |
| Kódování komprese  |gzip, brotli |Gzip |gzip, deflaci, bzip2, brotili  |gzip, deflaci, bzip2, brotili  |






