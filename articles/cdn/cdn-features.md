---
title: Porovnání funkcí produktu Azure Content Delivery Network (CDN) | Microsoft Docs
description: Seznamte se s podporovanými funkcemi v jednotlivých produktech Azure Content Delivery Network (CDN).
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0e57ae691bf4b07b8161bc343929510d6be041a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "81260509"
---
# <a name="compare-azure-cdn-product-features"></a>Porovnání funkcí produktu Azure CDN

Azure Content Delivery Network (CDN) zahrnuje čtyři produkty: **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Akamai**, **Azure CDN Standard od společnosti Verizon** a **Azure CDN Premium od společnosti Verizon**. Informace o migraci profilu **Azure CDN Standard od společnosti Verizon** na profil **Azure CDN Premium od společnosti Verizon** najdete v článku [Migrace profilu Azure CDN z úrovně Standard Verizon na úroveň Premium Verizon](cdn-migrate.md). Všimněte si, že když existuje cesta pro upgrade ze Standard Verizon na Premium Verizon, neexistuje žádný mechanismus převodu mezi ostatními produkty v tuto chvíli.

Následující tabulka obsahuje porovnání funkcí, které jsou dostupné v jednotlivých produktech.

| **Funkce a optimalizace výkonu** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** |
| --- | --- | --- | --- | --- |
| [Akcelerace dynamických webů](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Nabízeno přes [službu Azure front-dveří](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – adaptivní komprese obrázků](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Akcelerace dynamického webu – předběžné načtení objektů](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Obecná optimalizace webového doručování](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** vyberte tento typ optimalizace, pokud je průměrná velikost souboru menší než 10 MB.  | **&#x2713;** |  **&#x2713;** |
| [Optimalizace streamování videa](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | prostřednictvím obecného webového doručování | **&#x2713;**  | prostřednictvím obecného webového doručování |  prostřednictvím obecného webového doručování |
| [Optimalizace velkých souborů](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | prostřednictvím obecného webového doručování | **&#x2713;** vyberte tento typ optimalizace, pokud je průměrná velikost souboru větší než 10 MB.   | prostřednictvím obecného webového doručování |  prostřednictvím obecného webového doručování |
| Změnit typ optimalizace | |**&#x2713;** | | |
| Zdrojový port |Všechny porty TCP |[Povolené počáteční porty](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Všechny porty TCP |Všechny porty TCP |
| [Globální vyrovnávání zatížení serveru (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Rychlé vyprázdnění](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, vyprázdnit všechny a zástupné znaky nejsou aktuálně Azure CDN z Akamai. |**&#x2713;** |**&#x2713;** |
| [Předběžné načítání prostředku](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Nastavení mezipaměti nebo hlaviček (pomocí [pravidel ukládání do mezipaměti](cdn-caching-rules.md))  |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Přizpůsobitelný modul pro doručování obsahu založený na pravidlech |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md)  | | |**&#x2713;** pomocí [modulu pravidel](cdn-rules-engine.md) |
| Nastavení mezipaměti nebo hlaviček  |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md) | | |**&#x2713;** používání [modulu pravidel Premium](cdn-rules-engine.md) |
| Přesměrování nebo přepsání adresy URL |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md)  | | |**&#x2713;** používání [modulu pravidel Premium](cdn-rules-engine.md) |
| Pravidla mobilních zařízení  |**&#x2713;** pomocí [modulu Standard Rules](cdn-standard-rules-engine.md) | | |**&#x2713;** používání [modulu pravidel Premium](cdn-rules-engine.md) |
| [Ukládání řetězců dotazu do mezipaměti](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Duální sada protokolů IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Podpora HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Zabezpečení** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** | 
| Podpora HTTPS s koncovými body CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS pro vlastní doménu](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** vyžaduje přímý záznam CNAME, aby bylo možné povolit |**&#x2713;** |**&#x2713;** |
| [Podpora vlastních názvů domén](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geografické filtrování](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ověřování tokenu](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Ochrana DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Používání vlastního certifikátu](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Podporované verze TLS | TLS 1,2, TLS 1.0/1.1 – [konfigurovatelný](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analýzy a generování sestav** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** | 
| [Diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Základní sestavy z Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Vlastní sestavy z Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiky v reálném čase](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Výkon hraničních uzlů](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Výstrahy v reálném čase](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Jednoduché používání** | **Microsoft – standardní** | **Akamai úrovně Standard** | **Verizon úrovně Standard** | **Verizon úrovně Premium** | 
| Snadná integrace se službami Azure, jako jsou [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Správa prostřednictvím [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)nebo [PowerShellu](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Komprese typů MIME](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Pouze výchozí |Konfigurovatelné |Konfigurovatelné  |Konfigurovatelné  |
| Kódování komprese  |GZIP, brotli |GZIP |GZIP, Deflate, bzip2, brotili  |GZIP, Deflate, bzip2, brotili  |






